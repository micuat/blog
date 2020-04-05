---
layout: post
title:  "Tweet P5 Particle Study 39"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-05-tweet-p5-particle-study-39.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<81;N++)t[N]=[e=random(W),0,f=5*N,W,e,f];noStroke(),t.find(a=>{t.find(f=>{[,,,,q,s]=f,m=mag(x=a[4]-q,y=a[5]-s)+1,b=a[1]+=(m<20)*atan2(x,y)/m,u=a[4]+=cos(b)/N,v=a[5]+=sin(b)/N}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<81;N++)t[N]=[e=random(W),0,f=5*N,W,e,f];noStroke(),t.find(a=>{t.find(f=>{[,,,,q,s]=f,m=mag(x=a[4]-q,y=a[5]-s)+1,b=a[1]+=(m<20)*atan2(x,y)/m,u=a[4]+=cos(b)/N,v=a[5]+=sin(b)/N}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
</script>
