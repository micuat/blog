---
layout: post
title:  "Hydra Texture Study 24"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-19-hydra-texture-study-24.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=G79aA32rDNXou30J&code=c3JjKG8wKS5odWUoMC4wMikuc2NhbGUoLjkpLmxheWVyKHNoYXBlKDIlMkMwLjElMkMuMykubW9kdWxhdGVLYWxlaWQob3NjKE1hdGguUEklMkMxKS5kaWZmKGdyYWRpZW50KDEpLmNvbG9yKDIlMkMyJTJDMikpJTJDMSkubHVtYSgwLjMlMkMwLjMpLmNvbG9yKDElMkMwJTJDMCkuaHVlKC40KSkub3V0KCklMEE=)

{% highlight javascript %}
src(o0).hue(0.02).scale(.9).layer(shape(2,0.1,.3).modulateKaleid(osc(Math.PI,1).diff(gradient(1).color(2,2,2)),1).luma(0.3,0.3).color(1,0,0).hue(.4)).out()
{% endhighlight %}
