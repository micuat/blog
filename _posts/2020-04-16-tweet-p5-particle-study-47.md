---
layout: post
title:  "Tweet P5 Particle Study 47"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-16-tweet-p5-particle-study-47.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W);N<99;N++)t[N]=[f=5*N,0,W-f,W,sin(f)*M+M,cos(f)*M+M,0];t.find(f=>{f[1]*=.9,f[6]*=.9,t.find(([,,,,t,N])=>{b=1+mag(x=t-f[4],y=N-f[5])**2,u=f[1]+=x/b/9,v=f[6]+=y/b/9}),stroke(f),circle(f[4]+=u,f[5]+=v,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W).parent('p5sketch');N<99;N++)t[N]=[f=5*N,0,W-f,W,sin(f)*M+M,cos(f)*M+M,0];t.find(f=>{f[1]*=.9,f[6]*=.9,t.find(([,,,,t,N])=>{b=1+mag(x=t-f[4],y=N-f[5])**2,u=f[1]+=x/b/9,v=f[6]+=y/b/9}),stroke(f),circle(f[4]+=u,f[5]+=v,5)})};//#つぶやきProcessing
</script>
