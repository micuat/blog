---
layout: post
title:  "Tweet P5 Particle Study 27"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-12-tweet-p5-particle-study-27.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[0,0,N,W,0];setup=a=>createCanvas(W,W),draw=a=>{clear(),noStroke(),t['map']((a,r)=>{c++,b=t[c%N],a[0]+=cos(a[4]),a[2]+=sin(a[4]),a[1]=atan2(b[0],b[2])*mag(b[0],b[2]),a[4]=floor(b[1])/8,fill(a),circle(a[0]+N,a[2],a[1]/4)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
for(t=[],W=400,c=N=0;N<201;N++)t[N]=[0,0,N,W,0];setup=a=>createCanvas(W,W).parent('p5sketch'),draw=a=>{clear(),noStroke(),t['map']((a,r)=>{c++,b=t[c%N],a[0]+=cos(a[4]),a[2]+=sin(a[4]),a[1]=atan2(b[0],b[2])*mag(b[0],b[2]),a[4]=floor(b[1])/8,fill(a),circle(a[0]+N,a[2],a[1]/4)})};//#つぶやきProcessing
</script>
