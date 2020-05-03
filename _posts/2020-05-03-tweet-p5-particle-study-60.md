---
layout: post
title:  "Tweet P5 Particle Study 60"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-05-03-tweet-p5-particle-study-60.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[N=0],draw=o=>{N||(createCanvas(W=400,W),background(0)),t[N++%200]=[f=(r=random)(W),g=r(W),W-f,W,f,g,r(2)],t.find(r=>{stroke(r),fill(r),circle(u=r[4]+=cos(B=floor(8*noise(r[4]/99,r[5]/99))/4*PI)*r[6],v=r[5]+=sin(B)*r[6],4)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[N=0],draw=o=>{N||(createCanvas(W=400,W),background(0)),t[N++%200]=[f=(r=random)(W),g=r(W),W-f,W,f,g,r(2)],t.find(r=>{stroke(r),fill(r),circle(u=r[4]+=cos(B=floor(8*noise(r[4]/99,r[5]/99))/4*PI)*r[6],v=r[5]+=sin(B)*r[6],4)})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
