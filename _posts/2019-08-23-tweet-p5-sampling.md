---
layout: post
title:  "Tweet P5 Sampling"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2019-08-23-Tweet-P5-Sampling.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
let x,y,c,i
setup=_=>{createCanvas(a=600,a)
colorMode(HSB,1,1,1)
for(let j=0;j<a*a;j++,i=int(j/a))set(j%a,i,color(noise((j%a)/a*2,i/a*2),1,1))
updatePixels()
strokeWeight(90)}
draw=_=>{
colorMode(RGB)
c=get(x=random(a),y=random(a))
stroke(c)
point(x,y)}
//#つぶやきProcessing
{% endhighlight %}

<script>
//.parent("p5sketch");
let x,y,c,i
setup=_=>{createCanvas(a=600,a).parent("p5sketch")
colorMode(HSB,1,1,1)
for(let j=0;j<a*a;j++,i=int(j/a))set(j%a,i,color(noise((j%a)/a*2,i/a*2),1,1))
updatePixels()
strokeWeight(90)}
draw=_=>{
colorMode(RGB)
c=get(x=random(a),y=random(a))
stroke(c)
point(x,y)}
//#つぶやきProcessing
</script>