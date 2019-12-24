---
layout: post
title:  "Hydra Texture Study 10"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-24-hydra-texture-study-10.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=hEXHUrzWaL9B5avJ&code=b3NjKDYwJTJDMC4wMyUyQzEpLm1vZHVsYXRlKG8xKS5ibGVuZChvMCUyQzAuOSkubGF5ZXIob3NjKDYwJTJDMC4wMyUyQzEpLm1vZHVsYXRlKG5vaXNlKDIlMkMwLjAzKSkucm90YXRlKDEpLmx1bWEoMC43JTJDMCkuaHVlKDAuOCkpLm91dChvMSklMEFzcmMobzEpLm1vZHVsYXRlKHNyYyhvMCkuY29udHJhc3QoMikubHVtYSgwLjclMkMwLjEpJTJDMC4wMykuYmxlbmQobzAlMkMwLjkpLm91dChvMCklMEE=)

{% highlight javascript %}
osc(60,0.03,1).modulate(o1).blend(o0,0.9).layer(osc(60,0.03,1).modulate(noise(2,0.03)).rotate(1).luma(0.7,0).hue(0.8)).out(o1)
src(o1).modulate(src(o0).contrast(2).luma(0.7,0.1),0.03).blend(o0,0.9).out(o0)
{% endhighlight %}
