---
layout: post
title:  "Tweet P5 Particle Study 23"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-08-tweet-p5-particle-study-23.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],N=0;N<201;N++)t[N]=[0,0,0];setup=a=>createCanvas(W=400,W),draw=r=>{for(background(0,40),noStroke(),fill(255),i=1;i<N;i++)a=t[i],b=t[i-1],a[0]+=cos(b[2]/9),a[1]+=sin(b[2]/9),a[2]+=atan2(a[0],a[1])*mag(a[0],a[1]),circle(a[0]+N,a[1]+N,2)};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],N=0;N<201;N++)t[N]=[0,0,0];setup=a=>createCanvas(W=400,W).parent('p5sketch'),draw=r=>{for(background(0,40),noStroke(),fill(255),i=1;i<N;i++)a=t[i],b=t[i-1],a[0]+=cos(b[2]/9),a[1]+=sin(b[2]/9),a[2]+=atan2(a[0],a[1])*mag(a[0],a[1]),circle(a[0]+N,a[1]+N,2)};//#つぶやきProcessing
</script>
