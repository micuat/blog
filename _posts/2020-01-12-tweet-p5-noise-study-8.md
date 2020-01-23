---
layout: post
title:  "Tweet P5 Noise Study 8"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-12-tweet-p5-noise-study-8.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
setup=()=>createCanvas(w=200,w)+pixelDensity(1)+background(w,a=15e4)
draw=()=>{loadPixels()
p=pixels.copyWithin(8e4+3,9,2e4)
for(x=0;x<159e3;x+=9+p[a])p[x-p[int(x/w)]]=(x/4%w)%8<5?w:0;
p[a]=++p[a]%w
p.copyWithin(p[a]<<3,8e4-(p[a]<<1),a)
updatePixels()}//#つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)+background(w,a=15e4)
draw=()=>{loadPixels()
p=pixels.copyWithin(8e4+3,9,2e4)
for(x=0;x<159e3;x+=9+p[a])p[x-p[int(x/w)]]=(x/4%w)%8<5?w:0;
p[a]=++p[a]%w
p.copyWithin(p[a]<<3,8e4-(p[a]<<1),a)
updatePixels()}//#つぶやきprocessing
</script>