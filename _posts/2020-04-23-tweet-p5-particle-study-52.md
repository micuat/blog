---
layout: post
title:  "Tweet P5 Particle Study 52"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-23-tweet-p5-particle-study-52.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,draw=a=>{for(N||createCanvas(W=400,W),background(0,9);N<W;N++)t[N]=[f=(r=random)(W),g=r(W),W-f,W,f,g];t.find(a=>{t.find(([,,,,r,n])=>{m=mag(x=a[4]-r,y=a[5]-n)+1,m<9&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m)/m,v=a[5]+=sin(B)/m)}),stroke(a),circle(u,v,2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,draw=a=>{for(N||createCanvas(W=400,W),background(0,9);N<W;N++)t[N]=[f=(r=random)(W),g=r(W),W-f,W,f,g];t.find(a=>{t.find(([,,,,r,n])=>{m=mag(x=a[4]-r,y=a[5]-n)+1,m<9&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m)/m,v=a[5]+=sin(B)/m)}),stroke(a),circle(u,v,2)})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
