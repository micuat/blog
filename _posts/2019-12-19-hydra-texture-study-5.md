---
layout: post
title:  "Hydra Texture Study 5"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-19-hydra-texture-study-5.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=By7dmZHWzSk2usw5&code=b3NjKDIwJTJDMC4wMSUyQzEpLm1vZHVsYXRlUm90YXRlKG5vaXNlKDMlMkMwLjElMkMxKSUyQzEpLm1hc2soc2hhcGUoNCUyQzAuMSUyQzAuOTkpLnJlcGVhdCg0JTJDNCkpLmFkZChvMCUyQzAuOSkub3V0KG8xKSUwQXNyYyhvMSkubW9kdWxhdGUoc3JjKG8xKSUyQyUyMDAuMSkub3V0KG8yKSUwQXNvbGlkKCkubGF5ZXIoc3JjKG8xKS5kaWZmKG8yKSkuYmxlbmQobzAlMkMwLjkpLm91dChvMCk=)

{% highlight javascript %}
osc(20,0.01,1).modulateRotate(noise(3,0.1,1),1).mask(shape(4,0.1,0.99).repeat(4,4)).add(o0,0.9).out(o1)
src(o1).modulate(src(o1), 0.1).out(o2)
solid().layer(src(o1).diff(o2)).blend(o0,0.9).out(o0)
{% endhighlight %}
