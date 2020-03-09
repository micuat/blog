---
layout: post
title:  "Tweet P5 Particle Study 24"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-09-tweet-p5-particle-study-24.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],c=N=0;N<401;N++)t[N]=[0,0,0];setup=a=>createCanvas(N,N),draw=r=>{for(c+=1,background(0),noStroke(),fill(255),i=10;i<N;i++)a=t[i],b=t[max(0,i-c)],a[0]+=cos(b[2]/9),a[1]+=sin(b[2]/9),a[2]=atan2(a[0],a[1])*mag(a[0],a[1]),circle(5*a[0],5*a[1],2)};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],c=N=0;N<401;N++)t[N]=[0,0,0];setup=a=>createCanvas(N,N).parent('p5sketch'),draw=r=>{for(c+=1,background(0),noStroke(),fill(255),i=10;i<N;i++)a=t[i],b=t[max(0,i-c)],a[0]+=cos(b[2]/9),a[1]+=sin(b[2]/9),a[2]=atan2(a[0],a[1])*mag(a[0],a[1]),circle(5*a[0],5*a[1],2)};//#つぶやきProcessing
</script>
