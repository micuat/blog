---
layout: post
title:  "Tweet P5 Particle Study 46"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-15-tweet-p5-particle-study-46.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,draw=a=>{if(!N)for(createCanvas(W=400,W);N<99;N++)t[N]=[f=5*N,0,W-f,W,200*sin(f)+200,200*cos(f)+200];t.find(f=>{t.find(([,,,,t,N])=>{b=1+mag(x=t-f[4],y=N-f[5])**2,u=f[4]+=x/b*2,v=f[5]+=y/b*2}),stroke(f),circle(u,v,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,draw=a=>{if(!N)for(createCanvas(W=400,W).parent('p5sketch');N<99;N++)t[N]=[f=5*N,0,W-f,W,200*sin(f)+200,200*cos(f)+200];t.find(f=>{t.find(([,,,,t,N])=>{b=1+mag(x=t-f[4],y=N-f[5])**2,u=f[4]+=x/b*2,v=f[5]+=y/b*2}),stroke(f),circle(u,v,5)})};//#つぶやきProcessing
</script>
