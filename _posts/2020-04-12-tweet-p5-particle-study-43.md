---
layout: post
title:  "Tweet P5 Particle Study 43"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-12-tweet-p5-particle-study-43.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<81;N++)t[N]=[W-5*N,0,f=5*N,W,f,f];noStroke(),t.find(f=>{t.find(a=>{[,,,,q,s]=a,m=mag(x=f[4]-q,y=f[5]-s)+3,b=f[1]+=atan2(x,y)/m**2,u=f[4]+=cos(b)/N,v=f[5]+=sin(b)/N}),fill(f),circle(u,v,2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<81;N++)t[N]=[W-5*N,0,f=5*N,W,f,f];noStroke(),t.find(f=>{t.find(a=>{[,,,,q,s]=a,m=mag(x=f[4]-q,y=f[5]-s)+3,b=f[1]+=atan2(x,y)/m**2,u=f[4]+=cos(b)/N,v=f[5]+=sin(b)/N}),fill(f),circle(u,v,2)})};//#つぶやきProcessing
</script>