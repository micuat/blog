---
layout: post
title:  "Tweet P5 Particle Study 13"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-24-tweet-p5-particle-study-13.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=t=200
x=y=z=8
draw=_=>{N<t||createCanvas(W=400,W)
background(0)
t+=.06
for(i=0;i<N;i++)for(j=0;j<N;j++)I=i*N+j,[x,y,z]=[cos(I+(z))*sin(i/N+x),sin(I+(z))+sin(i/N+y),sin(I+x)+2*cos(I+y)],stroke(i,z*99,N-j),z>.2&&point(x*N/2+W/2,y*N/2+W/2),y+=t}//#つぶやきProcessing
{% endhighlight %}
