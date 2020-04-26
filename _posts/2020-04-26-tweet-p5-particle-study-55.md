---
layout: post
title:  "Tweet P5 Particle Study 55"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-26-tweet-p5-particle-study-55.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,draw=a=>{N||createCanvas(W=400,W),t[N++%200]=[0,0,0,60,150*cos(f=N/60)+200,150*sin(f)+200],t.find(a=>{t.find(([,,,,n,t])=>{m=mag(x=a[4]-n,y=a[5]-t)+1,m<9&&m>1&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m),v=a[5]+=sin(B),stroke(a),line(u,v,n,t))})})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,draw=a=>{N||createCanvas(W=400,W),t[N++%200]=[0,0,0,60,150*cos(f=N/60)+200,150*sin(f)+200],t.find(a=>{t.find(([,,,,n,t])=>{m=mag(x=a[4]-n,y=a[5]-t)+1,m<9&&m>1&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m),v=a[5]+=sin(B),stroke(a),line(u,v,n,t))})})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
