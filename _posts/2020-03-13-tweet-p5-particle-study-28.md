---
layout: post
title:  "Tweet P5 Particle Study 28"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-13-tweet-p5-particle-study-28.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[0,N,8,N,0];setup=N=>createCanvas(W,W),draw=a=>{clear(),noStroke(),t.find((a,r)=>{[j,k,l,m]=t[++c%N],a[0]=cos(1.5*a[4])*m,a[1]=sin(a[4])*m,a[4]=l/N,a[2]=atan2(j,k)*floor(mag(j,k)),fill(a),circle(j+N,k+N,l/4)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[0,N,8,N,0];setup=N=>createCanvas(W,W).parent('p5sketch'),draw=a=>{clear(),noStroke(),t.find((a,r)=>{[j,k,l,m]=t[++c%N],a[0]=cos(1.5*a[4])*m,a[1]=sin(a[4])*m,a[4]=l/N,a[2]=atan2(j,k)*floor(mag(j,k)),fill(a),circle(j+N,k+N,l/4)})};//#つぶやきProcessing
</script>
