---
layout: post
title:  "Tweet P5 Particle Study 31"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-16-tweet-p5-particle-study-31.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<41;N++)t[N]=[5*N,0,9*N];draw=a=>{c||createCanvas(W,W),background(W),t.find(a=>{[q,r,s]=t[++c%N],b=a[1]+=atan2(q,s)*mag(q,s),u=a[0]+=cos(b),v=a[2]+=sin(b),t.find(t=>{stroke(0,W-9*dist(u,v,x=t[0],y=t[2])),line(u+99,v,x+99,y)})})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<41;N++)t[N]=[5*N,0,9*N];draw=a=>{c||createCanvas(W,W).parent('p5sketch'),background(W),t.find(a=>{[q,r,s]=t[++c%N],b=a[1]+=atan2(q,s)*mag(q,s),u=a[0]+=cos(b),v=a[2]+=sin(b),t.find(t=>{stroke(0,W-9*dist(u,v,x=t[0],y=t[2])),line(u+99,v,x+99,y)})})};//#つぶやきProcessing
</script>
