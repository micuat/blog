---
layout: post
title:  "Tweet P5 Noise Study 9"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-16-tweet-p5-noise-study-9.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight java %}
setup=()=>createCanvas(w=200,w)+pixelDensity(1)+background(9,a=15e4)+rect(50,50,29,99)
draw=()=>{
loadPixels()
p=pixels
for(x=0e4;x<158e3;x+=1+p[a])p.copyWithin(x-w,x+p[a],x+w);
p.copyWithin(15e4,0,2e3)
updatePixels()}//#つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)+background(9,a=15e4)+rect(50,50,29,99)
draw=()=>{
loadPixels()
p=pixels
for(x=0e4;x<158e3;x+=1+p[a])p.copyWithin(x-w,x+p[a],x+w);
p.copyWithin(15e4,0,2e3)
updatePixels()}//#つぶやきprocessing
</script>