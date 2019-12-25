---
layout: post
title:  "Hydra Texture Study 11"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-25-hydra-texture-study-11.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=0qjzyAh4j2gxnvaM&code=dm9yb25vaSgxMCUyQzAuMSUyQzApLmx1bWEoMC4zJTJDMCkuY29sb3IoMSUyQzAuNSUyQzEpLm11bHQoc2hhcGUoNCUyQzAuNyUyQzAuMSkpLmFkZChzcmMobzEpLnNjYWxlKDAuOSklMkMtMSkub3V0KG8xKSUwQW9zYyg2JTJDMC4xJTJDMikua2FsZWlkKDEwMDApLm11bHQoc2hhcGUoMTAwMCUyQzAuNSUyQzAuNSkpLmxheWVyKHNyYyhvMSkubW9kdWxhdGVTY2FsZShvc2MoNTAlMkMwLjElMkMwKS5tb2R1bGF0ZVNjYWxlKG9zYyg0JTJDMC4zJTJDMCkpLmthbGVpZCgxMDAwKSUyQzAuMSkpLmJsZW5kKG8wJTJDLjUpLnNhdHVyYXRlKDEuNSkuaHVlKDAuMSkub3V0KCk=)

{% highlight javascript %}
voronoi(10,0.1,0).luma(0.3,0).color(1,0.5,1).mult(shape(4,0.7,0.1)).add(src(o1).scale(0.9),-1).out(o1)
osc(6,0.1,2).kaleid(1000).mult(shape(1000,0.5,0.5)).layer(src(o1).modulateScale(osc(50,0.1,0).modulateScale(osc(4,0.3,0)).kaleid(1000),0.1)).blend(o0,.5).saturate(1.5).hue(0.1).out()
{% endhighlight %}
