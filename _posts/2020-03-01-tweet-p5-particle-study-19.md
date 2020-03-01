---
layout: post
title:  "Tweet P5 Particle Study 19"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-01-tweet-p5-particle-study-19.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=200
x=y=z=w=t=0
draw=_=>{t||createCanvas(W=400,W)
background(0)
noStroke()
t+=.01
c=cos
for(i=0;i<N;i++)for(j=0;j<N;j+=9)[x,y,z,w]=[c(x)*sin(i+w),sin(y)+c(i+w),sin(i+x)+c(y),c(i+z)+t],o=abs(z-.5),fill(i,z*N,j,W*exp(-o*3)),circle(x*99+N,y*99+N,exp(o*2)+1)}//#つぶやきProcessing
{% endhighlight %}
