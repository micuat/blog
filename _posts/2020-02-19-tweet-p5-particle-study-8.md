---
layout: post
title:  "Tweet P5 Particle Study 8"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-19-tweet-p5-particle-study-8.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience.

{% highlight javascript %}
N=200
q={x:.01,y:0,z:0}
setup=_=>createCanvas(W=540,W)
draw=_=>{
noStroke()
background(0)
d=3e-4
let {x,y,z}=q
for(i=0;i<N;i++)for(j=0;j<N;j++)u=9*(y-x)*d,v=(x*(47-z)-y)*d,z+=(x*y-2*z)*d,x+=u,y+=v,fill(i,j,N),circle(x*9+W/2,y*9+W/2,2),j<30&&i<1&&(q={x,y,z})}//#つぶやきProcessing
{% endhighlight %}
