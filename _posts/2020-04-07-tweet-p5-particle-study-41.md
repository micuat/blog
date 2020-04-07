---
layout: post
title:  "Tweet P5 Particle Study 41"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-07-tweet-p5-particle-study-41.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<41;N++)t[N]=[W-N,0,f=9*N,W,N%2?f:W-f,f];clear(),t.find(a=>{t.find(f=>{[,,,,q,s]=f,m=mag(x=a[4]-q,y=a[5]-s)+1,b=a[1]+=atan2(x,y)/m,a[3]=W-4*m,stroke(a),line(a[4]+=cos(b)/N,a[5]+=sin(b)/N,q,s)})})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<41;N++)t[N]=[W-N,0,f=9*N,W,N%2?f:W-f,f];clear(),t.find(a=>{t.find(f=>{[,,,,q,s]=f,m=mag(x=a[4]-q,y=a[5]-s)+1,b=a[1]+=atan2(x,y)/m,a[3]=W-4*m,stroke(a),line(a[4]+=cos(b)/N,a[5]+=sin(b)/N,q,s)})})};//#つぶやきProcessing
</script>
