---
layout: post
title:  "Tweet P5 Particle Study 30"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-15-tweet-p5-particle-study-30.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[N,0,N%2?N:W-N];draw=a=>{c||createCanvas(W,W),background(W,30),noStroke(),t.find(a=>{[q,r,s]=t[++c%N],b=a[1]+=atan2(a[0]-q,a[2]-s)*(mag(a[0]-q,a[2]-s)/32),a[0]+=cos(b),a[2]+=sin(b),fill(a),circle(a[0]+99,a[2],2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[N,0,N%2?N:W-N];draw=a=>{c||createCanvas(W,W).parent('p5sketch'),background(W,30),noStroke(),t.find(a=>{[q,r,s]=t[++c%N],b=a[1]+=atan2(a[0]-q,a[2]-s)*(mag(a[0]-q,a[2]-s)/32),a[0]+=cos(b),a[2]+=sin(b),fill(a),circle(a[0]+99,a[2],2)})};//#つぶやきProcessing
</script>
