---
layout: post
title:  "Tweet P5 Particle Study 9"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-20-tweet-p5-particle-study-9.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=t=200
u=v=0
draw=_=>{N<t||createCanvas(W=400,W)
background(0)
x=u
y=v
r=.05
t+=r
noStroke()
for(i=0;i<N;i++)for(j=0;j<N;j++)[x,y]=[cos(r*i+y)+cos(r*i+x),sin(r*i+y)+sin(r*i+x)],fill(i,j,N),circle(x*N/2+N,y*N/2+N,2),x+=t,i<20&&(u=x,y=v)}//#つぶやきProcessing
{% endhighlight %}
