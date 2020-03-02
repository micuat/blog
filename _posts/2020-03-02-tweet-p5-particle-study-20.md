---
layout: post
title:  "Tweet P5 Particle Study 20"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-02-tweet-p5-particle-study-20.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=t=200
draw=_=>{N<t||createCanvas(W=400,W)
background(x=y=z=0)
noStroke()
t+=1
s=sin
c=cos
for(i=2;i<N;i+=4)for(j=0;j<N;j++)[x,y,z]=[c(i+z)+s(2*i+x),s(i+z)+c(2*i+x),c(x)],o=abs(z-c(t/9)/2),fill(i,z*N,j,W*exp(-o*4)),circle(x*99+N,y*99+N,exp(o*2)+1),x+=t/i}//#つぶやきProcessing
{% endhighlight %}
