---
layout: post
title:  "Tweet P5 Noise Study 4"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-08-tweet-p5-noise-study-4.png
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
draw=()=>{
t++%60<9?background(1,w):0
loadPixels()
p=pixels
for(x=1;x<39740;x++)
p[4*x+t%2-(p[4*x]>>3)]*=noise((x%w)/w,int(x/w)/w,(t+p[4*x-p[9]])*.1)>.5?10:5/p[4*x-p[3]]
updatePixels()
}
// #つぶやきprocessing
{% endhighlight %}

<script>
setup=()=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)
t=0
draw=()=>{
t++%60<9?background(1,w):0
loadPixels()
p=pixels
for(x=1;x<39740;x++)
p[4*x+t%2-(p[4*x]>>3)]*=noise((x%w)/w,int(x/w)/w,(t+p[4*x-p[9]])*.1)>.5?10:5/p[4*x-p[3]]
updatePixels()
}
</script>