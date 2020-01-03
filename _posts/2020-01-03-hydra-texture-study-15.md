---
layout: post
title:  "Hydra Texture Study 15"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-03-hydra-texture-study-15.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=i9Uaa8hDHGzDm12t&code=c3JjKG8yKS5odWUoMC4xMSkuYmxlbmQobzAlMkMwLjcpLmxheWVyKG9zYygzMCUyQzAuMDMlMkMxKS5yb3RhdGUoMC4zKS5tb2R1bGF0ZShvc2MoOCUyQzAuMikubW9kdWxhdGVTY2FsZShvc2MoMyUyQzEpKSUyQzAuMSkubXVsdChncmFkaWVudCgyKS5yZXBlYXQoMC43NSUyQzAuNzUpKS5jb2xvcig1JTJDNSUyQzUpLnNhdHVyYXRlKDIpLmh1ZSgwLjIpLmx1bWEoMS40JTJDMC4wMSkpLm91dChvMCklMEFzcmMobzApLm1vZHVsYXRlUGl4ZWxhdGUoc3JjKG8xKS5jb2xvcigxJTJDMCUyQzApLmFkZChzcmMobzEpLmNvbG9yKDAlMkMwJTJDMSklMkMtMSkuc2F0dXJhdGUoMCklMkMlMjAxMDAwJTJDJTIwNCkub3V0KG8xKSUwQXNyYyhvMSkuYmxlbmQoZ3JhZGllbnQoMikubW9kdWxhdGUoc3JjKG8yKS5zYXR1cmF0ZSgwKSkubW9kdWxhdGVSZXBlYXQoc3JjKG8yKS5zYXR1cmF0ZSgwKSklMkMwLjIpLm91dChvMiklMEFyZW5kZXIobzEp)

{% highlight javascript %}
src(o2).hue(0.11).blend(o0,0.7).layer(osc(30,0.03,1).rotate(0.3).modulate(osc(8,0.2).modulateScale(osc(3,1)),0.1).mult(gradient(2).repeat(0.75,0.75)).color(5,5,5).saturate(2).hue(0.2).luma(1.4,0.01)).out(o0)
src(o0).modulatePixelate(src(o1).color(1,0,0).add(src(o1).color(0,0,1),-1).saturate(0), 1000, 4).out(o1)
src(o1).blend(gradient(2).modulate(src(o2).saturate(0)).modulateRepeat(src(o2).saturate(0)),0.2).out(o2)
render(o1)
{% endhighlight %}

![another image]({{ site.baseurl }}/assets/images/2020-01-03-hydra-texture-study-15-2.png)

