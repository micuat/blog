---
layout: post
title:  "Tweet P5 Particle Study 11"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-22-tweet-p5-particle-study-11.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=t=200
u=v=8
draw=_=>{N<t||createCanvas(W=400,W)
background(0)
x=u
y=v
t+=.1
for(i=0;i<N;i++)for(j=0;j<N;j++)I=i*N+j,[x,y]=[cos(I+y)*sin(i/N+x),sin(I+y)+sin(i/N+x)],stroke(i,j,N-j),point(x*N/2+N,y*N/2+N),x+=t,i<(20*sin(t*3)+20)&&(u=x,y=v)}//#つぶやきProcessing
{% endhighlight %}
