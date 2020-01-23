---
layout: post
title:  "Tweet P5 Noise Study 6"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-10-tweet-p5-noise-study-6.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
setup=()=>createCanvas(w=200,w)+pixelDensity(1)
t=0
draw=()=>{t++%30<8?background(1,w):0
loadPixels()
p=pixels
for(x=0;x<159700;x+=1+p[w+1])p[x-p[int(x/w)]-p[p[x+2]]]=dist(x%w,int(x/w),p[1e4-p[p[x+2]]],p[3e4-p[x]])/(p[1e4+p[x-3]]+1)*w;
updatePixels()}
// #つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)
t=0
draw=()=>{t++%30<8?background(1,w):0
loadPixels()
p=pixels
for(x=0;x<159700;x+=1+p[w+1])p[x-p[int(x/w)]-p[p[x+2]]]=dist(x%w,int(x/w),p[1e4-p[p[x+2]]],p[3e4-p[x]])/(p[1e4+p[x-3]]+1)*w;
updatePixels()}
// #つぶやきprocessing
</script>