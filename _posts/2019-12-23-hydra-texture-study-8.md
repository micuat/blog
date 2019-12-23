---
layout: post
title:  "Hydra Texture Study 8"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-23-hydra-texture-study-8.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=7Pu79pRXFGJAreer&code=b3NjKDUwJTJDLTAuMDIlMkMxKS5odWUoLTAuNSkua2FsZWlkKDEwMDApLmx1bWEoMC42JTJDMCkubGF5ZXIob3NjKDMwJTJDMC4wJTJDMSkubW9kdWxhdGUobm9pc2UoNSklMkMwLjIpLmx1bWEoMC42JTJDMC41KSkuaHVlKC0wLjEpLm91dChvMSklMEFzb2xpZCgpLmxheWVyKHNyYyhvMSkpLm1vZHVsYXRlKHNyYyhvMCkuc2F0dXJhdGUoMCklMkMwLjA4KS5hZGQobzAlMkMwLjIpLmJsZW5kKG8wJTJDMC44KS5vdXQobzApJTBB)

{% highlight javascript %}
osc(50,-0.02,1).hue(-0.5).kaleid(1000).luma(0.6,0).layer(osc(30,0.0,1).modulate(noise(5),0.2).luma(0.6,0.5)).hue(-0.1).out(o1)
solid().layer(src(o1)).modulate(src(o0).saturate(0),0.08).add(o0,0.2).blend(o0,0.8).out(o0)
{% endhighlight %}
