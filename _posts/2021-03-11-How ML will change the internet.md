---
toc: true
layout: post
description: When every plausible idea is just an inference away.
categories: [ML, Large prior-free models, Transformer-VAE]
title: How ML will change the internet.
comments: true
hide: true
---

2 or 3 things I want every reader to remember:

1. Deep Learning allows interpolating between examples which gives rise to compelling outputs that haven't been seen before.
2. All interpolations a DL model can make already exist so just have to be searched for.
3. Eventually this search will allow all concepts to be interpolated over to produce compelling "new" content autonomasly.

Large scale Deep Learning models can combine abstract concepts in new ways.
GPT-3 can make stories while DALL-E can can create compelling new images.
These models are improving fast so I want to explore what improved versions of these systems will do and how they will change the internet.

## The state of the art in content generation

The best AI content generators use large models trained on huge datasets in an unsupervised manner.
This involves getting a huge corpus of data from the internet (text, images, etc) and training a model to recreate it.

In practice deep learning models learn by storing all their training data in a "latent space".
This is a map where content is arranged in terms of its properties (e.g. going left words get angrier, pictures become more cat like as you move up, etc).
To create new content we can simply adjust our positions to get new combinations of properties.

![.]({{ site.baseurl }}/images/face_interpolation.jpg "Moving between 2 training examples.")

Unlike Google Search which stores indexes and searches for results, a deep learning model combines aspects of existing content to make new results.
A great example of this is DALL-E which works like Google image search on steroids, interpolating between relevent results rather than indexing them.

Both are results for the prompt "a road sign with an image of a blue strawberry":

![![.]({{ site.baseurl }}/images/Google a road sign with an image of a blue strawberry.png)]({{ site.baseurl }}/images/Google a road sign with an image of a blue strawberry.png "Google")

![![.]({{ site.baseurl }}/images/DALL-E a road sign with an image of a blue strawberry.png)]({{ site.baseurl }}/images/DALL-E a road sign with an image of a blue strawberry.png "DALL-E")

Notice that Google's results are not relevent.
It is perfectly plausible for their to be "a road sign with an image of a blue strawberry" but there aren't many on the internet.

Meanwhile every one of DALL-E's generations are relevent.
Of course producing these outputs takes a long time and is currently very expensive.
But its clear that soon generating content will be easier than searching for it.

Once this happens we will no longer think of something being on or off the internet.
The idea that someone has their "nudes posted online" will be a meaningless phrase as you will inevitably generate nudes as you search for them.

{% include info.html text="People will understand that for many domains all concievable ideas are just a search away." type="primary" %}

Every picture of a painting, family member, home and more will be a quick search away.
The only value in "making" images will be in finding the right one, to search latent space to best fulfil someones needs.

GPT-3 is trained to complete text prompts, it interpolates on a huge dataset of web text to create plausible web content.

Here ideas and concepts can be merged to reason & write stories like in this hilarious example:

[![]({{ site.baseurl }}/images/Shannon-Potter.png "Read the full story.")](https://twitter.com/jonathanfly/status/1284082774325039105?s=20)

Already people are generating articles using GPT-3 by giving manual prompts ([example](https://liamp.substack.com/p/my-gpt-3-blog-got-26-thousand-visitors)).

Eventually finding prompts will also be automated.

It is important to keep in mind that GPT-3 and DALL-E are almost the same model.
The only difference is that DALL-E uses a VQ-VAE to compress images into token-like sequences.
You can think of this as a learned compiler for images with GPT-3 acting as the programmer.

This means we can apply the DALL-E format `{content search query} {VQ-VAE tokens}` to any form of data.
Expect VQ-VAEs to allow a future GPT model to generate any form of content letting it generate full rendered web pages rather then just text.

## Coming Apart

Before the internet people lived more similar lives. You would read one of a few newspapers and watch one of a few shows.
Overtime content generation has been democratised allowing anyone to publish content going from a few TV shows to hundreds of shows to millions of YouTube creators.

This trend has no sign of slowing down. In the future people will look at us akin to people living in the 1960's. Leading dull, samey lives without interesting content.

## The Future

We should realise that online content today is still very sparse and that there's a huge range of ideas yet to be explored.

By interpolating on existing content we can make new articles, videos, images, etc from different perspectives to make new combinations of ideas.
Some of these will be strictly to user descriptions while others will be discovered by exploring latent space.

In the future YouTube, Twitter, etc will be analysed by algorithms that see which viewerships are underserved and interpolate on real content to give their prefered perspectives.
Eventually this will extend to the whole internet where any new content produced is interpolated on several times over to create hundreds of new variations automatically.

First news outlets will be automated followed by musicsians & YouTubers also.
Human content creators will cry foul as their content is predictably remixed to other audiences likely with better veiwership.

Eventually a new internet will emerge.
One where rather than searching for a peice of content hand-made by a person, all content is generated to suite our individual needs.
This will feel like we each have our own internet.
One where all content matches just what we want.

## What you can do now.

So if the **infocalapse** is coming how can you take advantage of this?
The best thing to do is get involved on these early AI content generating tools.
Some of these are completely autonomous and some require a human in the loop.
Here are some ideas I've been considering.

### A new Google

What if a Google search interpolated between top results rather than linking to them?

Instead of serving a list of results it would generate a result page.
You could then use the search box to iteratively modify it to better fit your needs.

Is your blog post to high level? Do you wish this article was more funny? 
Enter that into a search box at the top and semantic interpolations will be applied to get more details from another post or just change the style.

### Transformer-VAEs

To make a new Google your probably going to need a [Transformer-VAE](https://github.com/Fraser-Greenlee/transformer-vae).

I've just released a project that allows using the transformer to interpolate over text and small images.
Hopefully once I try training at scale I'll be able to interpolate over entire documents.

It can also be applied to images & music. [PICO-8](https://www.lexaloffle.com/pico-8.php) has a ton of open source video game art, music & code, perhaps a VAE could interpolate over entire games?

### Auto ArtBreeder

[ArtBreeder](https://www.artbreeder.com) uses a mix of VAE's & GAN's to generate images by combining latent variables of existing images to interpolate between them.
Images are interpolated several times over to generate novel and strickingly different images.

This is different to most content generators in that you can't see an output image and easily know how to find it in latent space.
In the future algorithms will be needed to discover new images in these spaces ([see Kenith Stanley's talks for more info](https://youtu.be/lhYGXYeMq_E)).
Perhaps processing interpolated images with OpenAI's new CLIP model will allow discovering new images?

The final result would allow for fully automanus brainstorming.
Use this to prompt a larger model and you could get some compelling & creative outputs.

### Other Ideas

These ideas work great for very flexible mediums like images and text where slight mstakes can go unnoticed.

Could some clever fixes allow applying them to strict domains like program synthesis?
Maybe you could use latent variables to add/remove concepts in a Python function?
