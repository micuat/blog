---
layout: post
title:  "Tweet P5 Particle Study 7"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-18-tweet-p5-particle-study-7.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=t=x=y=200
setup=_=>createCanvas(W=540,W)
draw=_=>{
noStroke()
background(0,30)
t+=.1
for(i=0;i<N;i++)for(j=0;j<N;j++)r=PI/N,I=i+j/N,u=sin(r*I+y)+cos(r*I+x),v=sin(r*I+y)+sin(r*I+x),x=u+t,y=v,fill(i,j,99),circle(u*N/2+W/2,v*N/2+W/2,2)}//#つぶやきProcessing
{% endhighlight %}
