---
layout: post
title:  "Hydra Texture Study 14"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-02-hydra-texture-study-14.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=PS8MfHHQWZxgdw38&code=Y29sJTIwJTNEJTIwKG8pJTIwJTNEJTNFJTIwc2hhcGUoMTAwMCUyQzAuMyUyQzAuNSkuY29sb3IoMSUyQzAlMkMwKS5odWUoMC4yKS5tdWx0KG5vaXNlKDEwMDAlMkMxMDApLnRocmVzaCgwLjElMkMwLjApKS5zYXR1cmF0ZSg1KS5tb2R1bGF0ZVBpeGVsYXRlKHNyYyhvKS5zYXR1cmF0ZSgwKSUyQzEwMCUyQzIpJTBBY29sKG8yKS5sYXllcihzcmMobzIpLmx1bWEoMC41JTJDMC4wKS5odWUoMC4xNSkpLm91dChvMiklMEFzcmMobzIpLm91dChvMCklMEE=)

{% highlight javascript %}
col = (o) => shape(1000,0.3,0.5).color(1,0,0).hue(0.2).mult(noise(1000,100).thresh(0.1,0.0)).saturate(5).modulatePixelate(src(o).saturate(0),100,2)
col(o2).layer(src(o2).luma(0.5,0.0).hue(0.15)).out(o2)
src(o2).out(o0)
{% endhighlight %}
