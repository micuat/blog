---
layout: post
title:  "Hydra Texture Study 16"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-04-hydra-texture-study-16.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=iWNNXQfUTqUr0S7Y&code=c3JjKG8yKS5odWUoMC4wMSkubGF5ZXIob3NjKDIwJTJDMC4xJTJDMikubW9kdWxhdGVTY2FsZShvc2MoNCUyQzAuMyklMkMzKS5tb2R1bGF0ZVJvdGF0ZShub2lzZSgxJTJDMC4xKS5waXhlbGF0ZSgxJTJDMSklMkM0KS5sdW1hKDAuOCUyQzAuMCkuaHVlKDAuMykpLm91dChvMSklMEFzcmMobzEpLm1vZHVsYXRlUGl4ZWxhdGUoc3JjKG8yKS5wb3N0ZXJpemUoMzIpLmFkZChzcmMobzIpLmNvbG9yKDElMkMxJTJDMCklMkMtMSkubHVtYSgwLjAxJTJDMC4wKS5zYXR1cmF0ZSgwKSUyQzIwMDAlMkMyKS5vdXQobzIpJTBBc3JjKG8yKS5vdXQobzApJTBB)

{% highlight javascript %}
src(o2).hue(0.01).layer(osc(20,0.1,2).modulateScale(osc(4,0.3),3).modulateRotate(noise(1,0.1).pixelate(1,1),4).luma(0.8,0.0).hue(0.3)).out(o1)
src(o1).modulatePixelate(src(o2).posterize(32).add(src(o2).color(1,1,0),-1).luma(0.01,0.0).saturate(0),2000,2).out(o2)
src(o2).out(o0)
{% endhighlight %}
