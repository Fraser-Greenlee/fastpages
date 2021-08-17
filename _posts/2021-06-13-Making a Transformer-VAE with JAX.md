---
toc: true
layout: post
description: And how you can make new Transformers with JAX.
categories: [ML]
title: Making a Transformer-VAE with JAX.
comments: true
hide: true
---

JAX allows writing simple code that runs efficiently on TPUs.
These models can then operate on massive scales setting new benchmarks in performance.

For an intro to JAX & Flax please checkout these sources:

Intro to JAX & Flax,
{% include youtube.html content="https://youtu.be/XfoYk_Z5AkI" %}

Using Flax with Huggingface
{% include youtube.html content="https://youtu.be/fuAyUQcVzTY" %}

If you'd like to skip the post and just checkout the code you can see the source for training [Python](https://huggingface.co/flax-community/t5-vae-python) and [Wikipedia](https://huggingface.co/flax-community/t5-vae-wiki) VAEs.

## Making your own transformer

Its important to remember that deep neural nets performing the same task learn universal features [see circuits](https://distill.pub/2020/circuits/early-vision/).
This means all transformer architectures are largely equivilent with only small changes mattering.

With that in mind you should build your new model on existing Transformers with your unique change added to it.

For the Transformer-VAE I wanted to modify a T5 model to average pool the hidden states into one token.

{% include video.html url="/videos/avg-pool-t5.mp4" %}

And to autoencode that hidden token with a VAE.

![.]({{ site.baseurl }}/images/t5-deep-vae.png)

With a regularising loss that operates on each batch.

![.]({{ site.baseurl }}/images/t5-vae-loss-batch-2.png)

This would allow interpolating on sequences.

## How I made the Transformer

I started by making a repo to hold the model code.

This will be used by each trained model.

To make a new Flax Tranformer there's some boilerplate you'll need to start off with.

**[config.py](https://github.com/Fraser-Greenlee/t5-vae-flax/blob/main/src/config.py)**

Adds the extra configuration options your new transformer need:

```python
from transformers.configuration_utils import PretrainedConfig
from transformers import AutoConfig

class T5VaeConfig(PretrainedConfig):
    model_type = "transformer_vae"
    is_composition = True

    def __init__(
        self,
        # custom VAE config
        t5_model_name_or_path=None,
        n_latent_tokens=6,
        latent_token_size=32,
        ...
        # T5 config
        t5=dict(),
        vocab_size=32128,
        d_model=512,
        ...
        # end
        **kwargs,
    ):
        ...
        # You can load other configs as attributes to your own config
        self.t5 = AutoConfig.from_pretrained(t5_model_name_or_path, cache_dir=cache_dir)
        assertEqual(self.t5.model_type, "t5", "Need t5 model type for transformer_decoder.")
        self.t5.decoder_start_token_id = decoder_start_token_id
```

**[t5_vae.py](https://github.com/Fraser-Greenlee/t5-vae-flax/blob/main/src/t5_vae.py)**

This holds the main source code for the model.

```python
class FlaxT5VaeForAutoencodingModule(nn.Module):
    '''
        Runs the model inference.
    '''
    config: T5VaeConfig
    dtype: jnp.dtype = jnp.float32  # the dtype of the computation

    def setup(self):
        # Here we initialise the transformer we're building on
        self.t5 = FlaxT5ForConditionalGenerationModule(self.config.t5)
        # And load the VAE to be added.
        self.vae = VAE(self.config)

class FlaxT5VaePreTrainedModel(FlaxPreTrainedModel):
    '''
        An abstract class to handle weights initialization and a simple interface for downloading and loading pretrained models.
    '''
    config_class = T5VaeConfig
    base_model_prefix = "transformer"
    module_class: nn.Module = None

    def __init__(
        self,
        config: T5VaeConfig,
        input_shape: Tuple[int] = (1, 1),
        seed: int = 0,
        dtype: jnp.dtype = jnp.float32,
        **kwargs
    ):
        module = self.module_class(config=config, dtype=dtype, **kwargs)
        super().__init__(config, module, input_shape=input_shape, seed=seed, dtype=dtype)


class FlaxT5VaeForAutoencoding(FlaxT5VaePreTrainedModel):
    '''
        Holds the module class & prepared inputs for inference.
    '''
    module_class = FlaxT5VaeForAutoencodingModule
```

For the custom Flax model (in this case a VAE) its just a regular Flax model.

**[vae.py](https://github.com/Fraser-Greenlee/t5-vae-flax/blob/main/src/vae.py)**

```python
class VAE(nn.Module):
    config: T5VaeConfig
    dtype: jnp.dtype = jnp.float32  # the dtype of the computation

    def setup(self):
        self.encoder = VAE_ENCODER_MODELS[self.config.vae_encoder_model](self.config.latent_token_size, self.config.n_latent_tokens)
        self.decoder = VAE_DECODER_MODELS[self.config.vae_decoder_model](self.config.t5.d_model,  self.config.n_latent_tokens)

    def __call__(self, encoding=None, latent_codes=None):
        latent_codes = self.encode(encoding)
        return self.decode(latent_codes), latent_codes
```

For more Flax examples see [What does Flax look like](https://github.com/google/flax#what-does-flax-look-like).

## Training

Now that you've got your model code setup you can start training!

First make a [new huggingface model](https://huggingface.co/new).

This will hold the training code and model weights.
The model code will be added as a git submodule.

For a train script build on one of the [Huggingface examples](https://github.com/huggingface/transformers/tree/master/examples/flax).

## Debugging

JAX is efficient because it gets compiled into XLA. This is great for performance but it makes debugging tricky.

To get incremental runs of your code in Python you should build off the tests already in transformers.

See [test_modeling_flax_t5.py](https://github.com/huggingface/transformers/blob/master/tests/test_modeling_flax_t5.py).

## Run Training

During the HF sprint I got to use a TPUv3-8 which allowed training with an 800 batch size with N tokens per sentence.

Sadly this training only started 6 hours before the contest ended so it only saw 5% of wikipedia.

## Share your work

Finally you can share your new model using a Streamlit demo on Huggingface spaces.

Checkout the Transformer-VAE one [here](https://huggingface.co/spaces/flax-community/t5-vae).
