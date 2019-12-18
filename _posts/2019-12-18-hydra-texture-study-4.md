---
layout: post
title:  "Hydra Texture Study 4"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-18-hydra-texture-study-4.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=uxKe7KMpge0KIqUS&code=b3NjKDIwJTJDMC4wMSUyQzEpLmx1bWEoMC41JTJDMC41KS5hZGQob3NjKDIwJTJDMC4wMSUyQzEpLmx1bWEoMC41JTJDMC41KS5yb3RhdGUoMy4xNDE1JTJGMykpLnJvdGF0ZSgxJTJDMC4xKS5tdWx0KHNoYXBlKDMlMkMwLjElMkMwLjgpKS5yb3RhdGUoMSUyQy0wLjEpLnNhdHVyYXRlKDIpLmFkZChvMCUyQzAuNSkub3V0KG8xKSUwQXNyYyhvMSkubW9kdWxhdGVLYWxlaWQoc3JjKG8xKSUyQyUyMDEwLjIpLm91dChvMiklMEFzb2xpZCgpLmxheWVyKHNyYyhvMSkuZGlmZihvMikuYmxlbmQobzAlMkMwLjUpKS5vdXQobzAp)

{% highlight javascript %}
osc(20,0.01,1).luma(0.5,0.5).add(osc(20,0.01,1).luma(0.5,0.5).rotate(3.1415/3)).rotate(1,0.1).mult(shape(3,0.1,0.8)).rotate(1,-0.1).saturate(2).add(o0,0.5).out(o1)
src(o1).modulateKaleid(src(o1), 10.2).out(o2)
solid().layer(src(o1).diff(o2).blend(o0,0.5)).out(o0)
{% endhighlight %}
