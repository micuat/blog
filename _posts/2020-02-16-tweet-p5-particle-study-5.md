---
layout: post
title:  "Tweet P5 Particle Study 5"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-16-tweet-p5-particle-study-5.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. The code is mostly from [yuruyurau](https://twitter.com/yuruyurau), ported to JavaScript.

{% highlight javascript %}
N=200
t=x=y=0
draw=_=>{t==0&&createCanvas(W=540,W)+noStroke()
background(0)
t+=.1
for(i=0;i<N;i++)for(j=0;j<N;j++)r=.1,u=sin(tan(r*i/2)+y)+sin(r*i+x),v=cos(tan(r*i/2)+y)+cos(r*i+x),x=(u+t)*.9+x*.1,y=v*.9+y*.1,fill(i,j,99),circle(u*N/2+W/2,v*N/2+W/2,2)}//#つぶやきProcessing
{% endhighlight %}
