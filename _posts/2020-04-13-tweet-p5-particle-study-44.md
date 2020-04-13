---
layout: post
title:  "Tweet P5 Particle Study 44"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-13-tweet-p5-particle-study-44.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<81;N++)t[N]=[f=5*N,N,W-f,W,99*sin(N)+200,99*cos(N)+200];t.find(a=>{t.find(([,,,,c,t])=>{b=a[1]+=atan2(x=a[4]-c,y=a[5]-t)/(mag(x,y)+3),u=a[4]+=cos(b)/N,v=a[5]+=sin(b)/N}),stroke(a),circle(u,v,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<81;N++)t[N]=[f=5*N,N,W-f,W,99*sin(N)+200,99*cos(N)+200];t.find(a=>{t.find(([,,,,c,t])=>{b=a[1]+=atan2(x=a[4]-c,y=a[5]-t)/(mag(x,y)+3),u=a[4]+=cos(b)/N,v=a[5]+=sin(b)/N}),stroke(a),circle(u,v,5)})};//#つぶやきProcessing
</script>
