---
layout: post
title:  "Tweet P5 Particle Study 22"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-04-tweet-p5-particle-study-22.gif
description: "A sketch"
featured: true
comments: true
p5: false
---

Pre-rendered for better experience. Port of [Starfield from Coding Challenges](https://thecodingtrain.com/CodingChallenges/001-starfield.html).

{% highlight javascript %}
for(N=200,S=[],r=Math.random,i=0;i<N;i++)S[i]=[r()-.5,r()-.5,r()/9+1];setup=()=>createCanvas(400,400),draw=()=>{for(s of(translate(N,N),background(0),fill(255),S))stroke(255),line(s[0],s[1],s[0]*=s[2],s[1]*=s[2]),noStroke(),circle(s[0]%=N,s[1]%=N,3)};//#つぶやきProcessing
{% endhighlight %}
