---
layout: post
title:  "Hydra Texture Study 13"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-01-hydra-texture-study-13.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=f8prAGGVzztUrFbZ&code=Y29sJTIwJTNEJTIwKCklMjAlM0QlM0UlMjBub2lzZSg0JTJDMC4yKS5jb2xvcigwJTJDMSUyQzApLmh1ZSgtMC4xKS5tdWx0KG5vaXNlKDEwMDAlMkMxMDAwKS5sdW1hKDAuMSUyQzAuMCkpLnNhdHVyYXRlKDIwKS5tb2R1bGF0ZVBpeGVsYXRlKHNyYyhvMikuc2F0dXJhdGUoMCkubHVtYSgwLjIlMkMwLjApLmFkZChzb2xpZCgxJTJDMSklMkMwLjEpJTJDMTAwKSUwQXNyYyhvMikuc2F0dXJhdGUoMC45OSkuaHVlKDAuMSkubGF5ZXIoY29sKCkpLm91dChvMiklMEFzcmMobzIpLm91dCgpJTBB)

{% highlight javascript %}
col = () => noise(4,0.2).color(0,1,0).hue(-0.1).mult(noise(1000,1000).luma(0.1,0.0)).saturate(20).modulatePixelate(src(o2).saturate(0).luma(0.2,0.0).add(solid(1,1),0.1),100)
src(o2).saturate(0.99).hue(0.1).layer(col()).out(o2)
src(o2).out()
{% endhighlight %}
