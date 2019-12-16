---
layout: post
title:  "Hydra Texture Study 2"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-16-hydra-texture-study-2.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=qmL6u2H8THNp43Cq&code=bm9pc2UoMTAlMkMwLjMlMkMxMCkucGl4ZWxhdGUoMTAwJTJDMTAwKS5tb2R1bGF0ZVNjYWxlKG9zYyg1JTJDMC4xJTJDMjApKS5tb2R1bGF0ZShzcmMobzApJTJDMC4xKS5vdXQobzEpJTBBc3JjKG8xKS5tdWx0KG9zYygzJTJDMC4yJTJDMTApKS5hZGQoc3JjKG8wKS5jb2xvcmFtYSgwLjAxKSkub3V0KG8wKQ==)

{% highlight javascript %}
noise(10,0.3,10).pixelate(100,100).modulateScale(osc(5,0.1,20)).modulate(src(o0),0.1).out(o1)
src(o1).mult(osc(3,0.2,10)).add(src(o0).colorama(0.01)).out(o0)
{% endhighlight %}
