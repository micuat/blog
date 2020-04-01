---
layout: post
title:  "Tweet P5 Particle Study 36"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-01-tweet-p5-particle-study-36.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<41;N++)t[N]=[W-9*N,0,9*N];draw=a=>{c++||createCanvas(W,W),background(W,9),noStroke(),t.find(a=>{t.find(c=>{[q,r,s]=c,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),u=a[0]+=cos(b)/9,v=a[2]+=sin(b)/9}),fill(a),circle(u,v,4)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<41;N++)t[N]=[W-9*N,0,9*N];draw=a=>{c++||createCanvas(W,W).parent('p5sketch'),background(W,9),noStroke(),t.find(a=>{t.find(c=>{[q,r,s]=c,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),u=a[0]+=cos(b)/9,v=a[2]+=sin(b)/9}),fill(a),circle(u,v,4)})};//#つぶやきProcessing
</script>
