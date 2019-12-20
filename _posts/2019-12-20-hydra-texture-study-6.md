---
layout: post
title:  "Hydra Texture Study 6"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-20-hydra-texture-study-6.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=3ks6X9HfIfPBafu7&code=b3NjKDIwJTJDMC4wMSUyQzIpLnBpeGVsYXRlKDM2JTJDMSkubW9kdWxhdGVSb3RhdGUobm9pc2UoMyUyQzAuMSUyQzEpJTJDMSkubWFzayhub2lzZSgzKS50aHJlc2goMC4zKSkuYWRkKG8zJTJDMC44KS5vdXQobzEpJTBBc3JjKG8xKS5tb2R1bGF0ZShzcmMobzEpJTJDJTIwMC44KS5vdXQobzIpJTBBc29saWQoKS5sYXllcihzcmMobzEpLmRpZmYobzIpKS5ibGVuZChzcmMobzMpLnNjYWxlKDEuMDA1KSUyQzAuNikub3V0KG8zKSUwQXNyYyhvMykub3V0KG8wKQ==)

{% highlight javascript %}
osc(20,0.01,2).pixelate(36,1).modulateRotate(noise(3,0.1,1),1).mask(noise(3).thresh(0.3)).add(o3,0.8).out(o1)
src(o1).modulate(src(o1), 0.8).out(o2)
solid().layer(src(o1).diff(o2)).blend(src(o3).scale(1.005),0.6).out(o3)
src(o3).out(o0)
{% endhighlight %}
