---
layout: post
title:  "Hydra Texture Study 7"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-21-hydra-texture-study-7.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=av3ZHbhydoCJrlEs&code=dm9yb25vaSgyJTJDMC4xKS5hZGQodm9yb25vaSgyMCUyQzAuMikuY29udHJhc3QoMikuY29sb3IoMiUyQzIlMkMyKSkubW9kdWxhdGUobzMlMkMwLjIpLmJsZW5kKG8zJTJDMC45KS5vdXQobzMpJTBBc3JjKG8zKS5zaGlmdCgwLjUlMkMwJTJDMCUyQzApLm1vZHVsYXRlKG8yJTJDLTAuMikuc2hpZnQoMCUyQzAuNykub3V0KG8yKSUwQXNvbGlkKCkubGF5ZXIoc3JjKG8yKS5jb2xvcigyJTJDMiUyQzIpLmJsZW5kKG8wJTJDMC45KSkub3V0KG8wKSUwQQ==)

{% highlight javascript %}
voronoi(2,0.1).add(voronoi(20,0.2).contrast(2).color(2,2,2)).modulate(o3,0.2).blend(o3,0.9).out(o3)
src(o3).shift(0.5,0,0,0).modulate(o2,-0.2).shift(0,0.7).out(o2)
solid().layer(src(o2).color(2,2,2).blend(o0,0.9)).out(o0)
{% endhighlight %}
