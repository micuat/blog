---
layout: post
title:  "Hydra Texture Study 9"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-24-hydra-texture-study-9.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=VR2DTW2MPk1QYfWA&code=biUyMCUzRCUyMDUwJTNCJTBBc29saWQoMSUyQzElMkMxKS5tYXNrKHNoYXBlKDQlMkMwLjI1KS5zY2FsZSgxJTJDMSUyQzMpLnJlcGVhdChuJTJDbikubXVsdChvc2MoMjAlMkMzKS5yb3RhdGUoMSkucGl4ZWxhdGUobiUyQ24pKSkub3V0KG8xKSUwQXNvbGlkKCkuYWRkKHNyYyhvMSkuY29sb3IoMCUyQzAlMkMxKS5tb2R1bGF0ZShub2lzZSgyJTJDMC4xKSUyQzAuMDQpJTJDMSkuYWRkKHNyYyhvMSkuc2Nyb2xsWCgxJTJGbiUyRjMpLmNvbG9yKDAlMkMxJTJDMCkubW9kdWxhdGUobm9pc2UoMiUyQzAuMSklMkMwLjA2KSUyQzEpLmFkZChzcmMobzEpLnNjcm9sbFgoMiUyRm4lMkYzKS5jb2xvcigxJTJDMCUyQzApLm1vZHVsYXRlKG5vaXNlKDIlMkMwLjEpJTJDMC4wOCklMkMxKS5vdXQobzApJTBB)

{% highlight javascript %}
n = 50;
solid(1,1,1).mask(shape(4,0.25).scale(1,1,3).repeat(n,n).mult(osc(20,3).rotate(1).pixelate(n,n))).out(o1)
solid().add(src(o1).color(0,0,1).modulate(noise(2,0.1),0.04),1).add(src(o1).scrollX(1/n/3).color(0,1,0).modulate(noise(2,0.1),0.06),1).add(src(o1).scrollX(2/n/3).color(1,0,0).modulate(noise(2,0.1),0.08),1).out(o0)
{% endhighlight %}
