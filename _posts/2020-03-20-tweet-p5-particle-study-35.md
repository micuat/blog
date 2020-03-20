---
layout: post
title:  "Tweet P5 Particle Study 35"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-20-tweet-p5-particle-study-35.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<21;N++)t[N]=[W-20*N,0,20*N,0];draw=a=>{c++||createCanvas(W,W),background(W),t.find(a=>t.find(t=>{[q,r,s]=t,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),a[3]=W-2*dist(u=a[0]+=cos(b)/3,v=a[2]+=sin(b)/3,q,s),stroke(a),line(u,v,q,s)}))};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<21;N++)t[N]=[W-20*N,0,20*N,0];draw=a=>{c++||createCanvas(W,W).parent('p5sketch'),background(W),t.find(a=>t.find(t=>{[q,r,s]=t,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),a[3]=W-2*dist(u=a[0]+=cos(b)/3,v=a[2]+=sin(b)/3,q,s),stroke(a),line(u,v,q,s)}))};//#つぶやきProcessing
</script>
