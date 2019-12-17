---
layout: post
title:  "Hydra Texture Study 3"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-17-hydra-texture-study-3.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=Fs8TRKKgvdkUMOTB&code=b3NjKDMwJTJDMC4wMSUyQzEpLmFkZChvc2MoMzAlMkMwLjAxJTJDMSkucm90YXRlKDEpKS5tdWx0KG5vaXNlKDIpLmNvbnRyYXN0KDIpJTJDMC40KS5ibGVuZChvMCUyQzAuNSkub3V0KG8xKSUwQXNyYyhvMSkubW9kdWxhdGUoc3JjKG8xKSUyQzAuMTUpLm91dChvMiklMEFzcmMobzEpLmRpZmYobzIpLmJsZW5kKG8wJTJDMC41KS5vdXQobzAp)

{% highlight javascript %}
osc(30,0.01,1).add(osc(30,0.01,1).rotate(1)).mult(noise(2).contrast(2),0.4).blend(o0,0.5).out(o1)
src(o1).modulate(src(o1),0.15).out(o2)
src(o1).diff(o2).blend(o0,0.5).out(o0)
{% endhighlight %}
