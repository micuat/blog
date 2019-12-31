---
layout: post
title:  "Hydra Texture Study 12"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-31-hydra-texture-study-12.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=of6kp0vhGxTDiGgK&code=Y29sJTIwJTNEJTIwKCklMjAlM0QlM0UlMjBncmFkaWVudCgxMCkubW9kdWxhdGVTY3JvbGxYKG5vaXNlKDUlMkMwLjIpLnBpeGVsYXRlKDElMkMxKSUyQzEpLm1vZHVsYXRlU2Nyb2xsWShub2lzZSgxMCUyQzAuMSkucGl4ZWxhdGUoMSUyQzEpJTJDMSkubXVsdChub2lzZSgxMDAwJTJDMTAwMCkudGhyZXNoKDAuOCUyQzAuMCkubHVtYSgwLjQlMkMwLjApKS5zYXR1cmF0ZSgyMCkubW9kdWxhdGVQaXhlbGF0ZShub2lzZSgxMDAwJTJDMC4yKS5waXhlbGF0ZSg0JTJDNCkuYWRkKHNvbGlkKDElMkMxKSUyQzAuMSklMkM0MDApJTBBc3JjKG8yKS5zYXR1cmF0ZSgwLjk5KS5sYXllcihjb2woKSkub3V0KG8yKSUwQXNyYyhvMikub3V0KCklMEE=)

{% highlight javascript %}
col = () => gradient(10).modulateScrollX(noise(5,0.2).pixelate(1,1),1).modulateScrollY(noise(10,0.1).pixelate(1,1),1).mult(noise(1000,1000).thresh(0.8,0.0).luma(0.4,0.0)).saturate(20).modulatePixelate(noise(1000,0.2).pixelate(4,4).add(solid(1,1),0.1),400)
src(o2).saturate(0.99).layer(col()).out(o2)
src(o2).out()
{% endhighlight %}
