---
layout: post
title:  "Tweet P5 Particle Study 57"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-28-tweet-p5-particle-study-57.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[N=0],draw=a=>{N||(createCanvas(W=400,W),background(W)),t[N++%(n=200)]=[N%n,0,n-N%n,60,4*N%W,35*floor(N/100)],t.find(n=>{t.find(([,,,,a,t])=>{m=mag(x=n[4]-a,y=n[5]-t),m<7&&m>0&&(stroke(n),line(n[4]+=cos(B=n[1]+=atan2(x,y)/m),n[5]+=sin(B),a,t))})})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[N=0],draw=a=>{N||(createCanvas(W=400,W),background(W)),t[N++%(n=200)]=[N%n,0,n-N%n,60,4*N%W,35*floor(N/100)],t.find(n=>{t.find(([,,,,a,t])=>{m=mag(x=n[4]-a,y=n[5]-t),m<7&&m>0&&(stroke(n),line(n[4]+=cos(B=n[1]+=atan2(x,y)/m),n[5]+=sin(B),a,t))})})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
