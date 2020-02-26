---
layout: post
title:  "Tweet P5 Particle Study 15"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-26-tweet-p5-particle-study-15.gif
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
noStroke()
t+=.1
c=cos
for(i=0;i<N;i++)for(j=0;j<N;j++)I=i+j/N,[x,y,z]=[c(z)+sin(i+x),sin(I+z)+c(i+x),sin(I+x)+c(I+y)],o=c(z),fill(i,z*99,j,N*o*o),circle(x*N+N,y*N+N,2*pow(2-o,2)),z+=t}//#つぶやきProcessing
{% endhighlight %}
