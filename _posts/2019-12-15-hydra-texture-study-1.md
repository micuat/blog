---
layout: post
title:  "Hydra Texture Study 1"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-15-hydra-texture-study-1.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=ob5pAJjjHJlgltp4&code=c2hhcGUoMykubW9kdWxhdGUob3NjKDIlMkMlMjAwLjElMkMlMjAwLjgpLmNvbG9yKDEuJTJDMCUyQyUyMC0xKS5yb3RhdGUoMC4zMCUyQyUyMDAuMSkucGl4ZWxhdGUoMiUyQyUyMDIwMDApLm1vZHVsYXRlSHVlKG9zYygxMCUyQzAuMSUyQzEpJTJDMTAwMDApKS5vdXQobzEpJTBBc2hhcGUoMykubW9kdWxhdGUob3NjKCgpJTNEJTNFMiUyQjAuMDEqTWF0aC5zaW4odGltZSowLjEpJTJDJTIwMC4xJTJDJTIwMC44KS5jb2xvcigxLiUyQzAlMkMlMjAtMSkucm90YXRlKDAuMzAlMkMlMjAwLjEpLnBpeGVsYXRlKDIlMkMlMjAyMDAwKS5tb2R1bGF0ZUh1ZShvc2MoMTAlMkMwLjElMkMxKSUyQzEwMDAwKSkub3V0KG8yKSUwQXNyYyhvMSkuY29sb3IoMSUyQzAlMkMwKS5hZGQoc3JjKG8yKS5jb2xvcigwJTJDMSUyQzEpJTJDMSkub3V0KG8wKSUwQQ==)

{% highlight javascript %}
shape(3).modulate(osc(2, 0.1, 0.8).color(1.,0, -1).rotate(0.30, 0.1).pixelate(2, 2000).modulateHue(osc(10,0.1,1),10000)).out(o1)
shape(3).modulate(osc(()=>2+0.01*Math.sin(time*0.1), 0.1, 0.8).color(1.,0, -1).rotate(0.30, 0.1).pixelate(2, 2000).modulateHue(osc(10,0.1,1),10000)).out(o2)
src(o1).color(1,0,0).add(src(o2).color(0,1,1),1).out(o0)
{% endhighlight %}
