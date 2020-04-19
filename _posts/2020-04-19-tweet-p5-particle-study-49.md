---
layout: post
title:  "Tweet P5 Particle Study 49"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-19-tweet-p5-particle-study-49.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W);N<99;)t[N]=[A=random(M),0,f=4*N++,W,sin(f)*A+M,cos(f)*A+M,0];t.find(f=>{t.find(([,,,,a,r])=>{b=1+mag(x=a-f[4],y=r-f[5])**2,f[1]+=x/b,f[6]+=y/b}),stroke(f),circle(f[4]+=f[1]/=3,f[5]+=f[6]/=3,5)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,M=200,draw=a=>{if(!N)for(createCanvas(W=400,W);N<99;)t[N]=[A=random(M),0,f=4*N++,W,sin(f)*A+M,cos(f)*A+M,0];t.find(f=>{t.find(([,,,,a,r])=>{b=1+mag(x=a-f[4],y=r-f[5])**2,f[1]+=x/b,f[6]+=y/b}),stroke(f),circle(f[4]+=f[1]/=3,f[5]+=f[6]/=3,5)})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
