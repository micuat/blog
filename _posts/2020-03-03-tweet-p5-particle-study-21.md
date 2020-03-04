---
layout: post
title:  "Tweet P5 Particle Study 21"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-03-tweet-p5-particle-study-21.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience.

{% highlight javascript %}
N=200
q={x:.01,y:0,z:0}
setup=_=>createCanvas(W=400,W)
draw=_=>{
noStroke()
background(0)
let{x,y,z}=q
for(i=1;i<N;i+=6)for(j=0;j<N;j+=2)d=.02/i,[x,y,z]=[x+15*(y-x)*d,y+(x*(28-z)-y)*d,z+(x*y-2*z)*d],fill(i,j,N-z*2),circle(x*8+N,y*8+N,z/9),j+i<7&&(q={x,y,z})}//#つぶやきProcessing
{% endhighlight %}
