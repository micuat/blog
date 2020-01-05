---
layout: post
title:  "Tweet P5 Noise Study 1"
author: naoto
categories: [ sketch ]
tags: [ processing, tinysketch ]
image: assets/images/2020-01-05-tweet-p5-noise-study-1.png
description: "A sketch"
featured: true
comments: true
p5: false
---

{% highlight java %}
int x, y;float t;void setup(){size(720,720);}
void draw(){t+=.04;loadPixels();for(y=0;y<719;y++)for(x=0;x<720;x++)
pixels[int(red(pixels[y*720+x]))+y*720+x]=lerpColor(pixels[y*720+x],color(noise(x*.01,y*.01,t)>0.5?255:0),0.5);
updatePixels();}
// #つぶやきProcessing
{% endhighlight %}
