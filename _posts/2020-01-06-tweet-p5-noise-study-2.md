---
layout: post
title:  "Tweet P5 Noise Study 2"
author: naoto
categories: [ sketch ]
tags: [ processing, tinysketch ]
image: assets/images/2020-01-06-tweet-p5-noise-study-2.png
description: "A sketch"
featured: true
comments: true
p5: false
---

{% highlight java %}
float t;void setup(){size(720,720);}
void draw(){t+=.07;loadPixels();for(int a=0;a<517680;a++)
pixels[int(red(pixels[a]))+a]=
lerpColor(pixels[a]>>1,color(noise(a%720*.002,a/720*.002,t)>.5?255:0)>>2,sin(t)*.4+.5);
updatePixels();}
// #つぶやきProcessing
{% endhighlight %}
