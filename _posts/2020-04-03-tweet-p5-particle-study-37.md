---
layout: post
title:  "Tweet P5 Particle Study 37"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-03-tweet-p5-particle-study-37.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,M=200,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<41;N++)t[N]=[99*cos(N/7)+M,0,99*sin(N/7)+M];noStroke(),t.find(a=>{t.find(c=>{[q,r,s]=c,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),u=a[0]+=cos(b)/99,v=a[2]+=sin(b)/99}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,M=200,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<41;N++)t[N]=[99*cos(N/7)+M,0,99*sin(N/7)+M];noStroke(),t.find(a=>{t.find(c=>{[q,r,s]=c,b=a[1]+=atan2(x=a[0]-q,y=a[2]-s)/(1+mag(x,y)),u=a[0]+=cos(b)/99,v=a[2]+=sin(b)/99}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
</script>
