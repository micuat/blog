---
layout: post
title:  "Tweet P5 Particle Study 48"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-17-tweet-p5-particle-study-48.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W);N<99;N++)t[N]=[4*N,0,W-4*N,W,sin(f=N**.5)*M+M,cos(f)*M+M,0];t.find(N=>{t.find(([,,,,f,t])=>{b=1+mag(x=f-N[4],y=t-N[5])**2,N[1]+=x/b,N[6]+=y/b}),u=N[1]/=2,v=N[6]/=2,stroke(N),circle(N[4]+=u,N[5]+=v,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W).parent('p5sketch');N<99;N++)t[N]=[4*N,0,W-4*N,W,sin(f=N**.5)*M+M,cos(f)*M+M,0];t.find(N=>{t.find(([,,,,f,t])=>{b=1+mag(x=f-N[4],y=t-N[5])**2,N[1]+=x/b,N[6]+=y/b}),u=N[1]/=2,v=N[6]/=2,stroke(N),circle(N[4]+=u,N[5]+=v,5)})};//#つぶやきProcessing
</script>
