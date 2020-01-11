---
layout: post
title:  "Tweet P5 Noise Study 7"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-11-tweet-p5-noise-study-7.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight java %}
setup=()=>createCanvas(w=200,w)+pixelDensity(1)+background(1)
draw=()=>{loadPixels()
p=pixels.copyWithin(8e4,9,3e4)
p.copyWithin(p[8e4+9]*p[15e4],w*w,w*(2*w+ ++p[15e4]))
for(x=1;x<159e3;x+=2+p[15e4])p[x-p[int(x/w)]]=dist(x/4%w,int(x/800),9,w);
updatePixels()}//#つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)+background(1)
draw=()=>{loadPixels()
p=pixels.copyWithin(8e4,9,3e4)
p.copyWithin(p[8e4+9]*p[15e4],w*w,w*(2*w+ ++p[15e4]))
for(x=1;x<159e3;x+=2+p[15e4])p[x-p[int(x/w)]]=dist(x/4%w,int(x/800),9,w);
updatePixels()}//#つぶやきprocessing
</script>