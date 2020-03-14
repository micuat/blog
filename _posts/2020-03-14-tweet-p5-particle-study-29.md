---
layout: post
title:  "Tweet P5 Particle Study 29"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-14-tweet-p5-particle-study-29.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[W-N,0,2*N];draw=o=>{c||createCanvas(W,W),(d=drawingContext).shadowBlur=5,noStroke(),t.find(o=>{d.shadowColor=color([q,r,s]=t[c++%N]),o[0]+=cos(b=o[1]=atan2(q,s)*mag(q,s)/30),o[2]+=sin(b),fill(o),circle(o[0]-99,o[2],2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[W-N,0,2*N];draw=o=>{c||createCanvas(W,W).parent('p5sketch'),(d=drawingContext).shadowBlur=5,noStroke(),t.find(o=>{d.shadowColor=color([q,r,s]=t[c++%N]),o[0]+=cos(b=o[1]=atan2(q,s)*mag(q,s)/30),o[2]+=sin(b),fill(o),circle(o[0]-99,o[2],2)})};//#つぶやきProcessing
</script>
