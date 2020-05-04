---
layout: post
title:  "Tweet P5 Particle Study 61"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-05-04-tweet-p5-particle-study-61.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[N=0],draw=a=>{N||(createCanvas(W=400,W),background(0)),t[N++%999]=[f=(r=random)(W),g=r(W),W-f,W,f,g,r(1)],t.find(r=>{stroke(r),fill(r),circle(r[4]+=cos(B=8*noise(r[4]/49,r[5]/49,r[6])/4*PI)*r[6],r[5]+=sin(B)*r[6],1)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[N=0],draw=a=>{N||(createCanvas(W=400,W),background(0)),t[N++%999]=[f=(r=random)(W),g=r(W),W-f,W,f,g,r(1)],t.find(r=>{stroke(r),fill(r),circle(r[4]+=cos(B=8*noise(r[4]/49,r[5]/49,r[6])/4*PI)*r[6],r[5]+=sin(B)*r[6],1)})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
