---
layout: post
title:  "Tweet P5 Noise Study 11"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-18-tweet-p5-noise-study-11.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight java %}
setup=()=>createCanvas(w=200,w)+pixelDensity(1)+background(9,a=15e4)
draw=()=>{ellipse(50,51,5)
loadPixels()
p=pixels
for(x=0e4;x<158e3;x+=9+p[a])p.copyWithin(x-w,x+p[a],x+w);
p.copyWithin(15e4,1,2e3)
p[w+2]>99?p.sort():0
updatePixels()}//#つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)+background(9,a=15e4)
draw=()=>{ellipse(50,51,5)
loadPixels()
p=pixels
for(x=0e4;x<158e3;x+=9+p[a])p.copyWithin(x-w,x+p[a],x+w);
p.copyWithin(15e4,1,2e3)
p[w+2]>99?p.sort():0
updatePixels()}//#つぶやきprocessing
</script>