---
layout: post
title:  "Tweet P5 Particle Study 26"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-11-tweet-p5-particle-study-26.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[N/4,0,N];setup=a=>createCanvas(W,W),draw=r=>{for(c++,clear(),noStroke(),i=0;i<N;i++)a=t[i],b=t[(i+c+N)%N],a[0]+=cos(a[1]/8),a[2]+=sin(a[1]/8),a[1]=atan2(b[0],b[2])*mag(b[0],b[2]),fill(a),circle(a[0]+N,a[2],a[1])};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[N/4,0,N];setup=a=>createCanvas(W,W).parent('p5sketch'),draw=r=>{for(c++,clear(),noStroke(),i=0;i<N;i++)a=t[i],b=t[(i+c+N)%N],a[0]+=cos(a[1]/8),a[2]+=sin(a[1]/8),a[1]=atan2(b[0],b[2])*mag(b[0],b[2]),fill(a),circle(a[0]+N,a[2],a[1])};//#つぶやきProcessing
</script>
