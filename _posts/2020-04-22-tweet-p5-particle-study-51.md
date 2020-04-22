---
layout: post
title:  "Tweet P5 Particle Study 51"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-22-tweet-p5-particle-study-51.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,draw=a=>{if(!N)for(createCanvas(W=400,W);N<41;N++)t[N]=[f=9*N,0,W-f,W,99*cos(N)+200,99*sin(N)+200];t.find(N=>{t.find(([,,,,t,a])=>{m=mag(x=N[4]-t,y=N[5]-a)**2+1,u=N[4]+=cos(B=int(b=N[1]+=atan2(x,y)/m))/m,v=N[5]+=sin(B)/m}),stroke(N),circle(u,v,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,draw=a=>{if(!N)for(createCanvas(W=400,W);N<41;N++)t[N]=[f=9*N,0,W-f,W,99*cos(N)+200,99*sin(N)+200];t.find(N=>{t.find(([,,,,t,a])=>{m=mag(x=N[4]-t,y=N[5]-a)**2+1,u=N[4]+=cos(B=int(b=N[1]+=atan2(x,y)/m))/m,v=N[5]+=sin(B)/m}),stroke(N),circle(u,v,5)})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
