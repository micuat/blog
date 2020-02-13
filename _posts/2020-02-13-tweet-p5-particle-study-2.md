---
layout: post
title:  "Tweet P5 Particle Study 2"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-13-tweet-p5-particle-study-2.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
s=[m=0]
draw=_=>{
createCanvas(b=400,b)
a=m++%255
colorMode(HSB)
e=b*sin(m/200)/2
s[m%80]=[e*cos(c=e/10+PI*(m%2))+200,e*sin(c)+200,m,0]
s['map'](_=>(_[3]+=.1,y=_[1]+=_[3],x=_[0]+sin(_[3])*20)+strokeWeight(9-_[3])+stroke(_[2]*2%b,b,b)+point(x,y))}//#つぶやきProcessing
{% endhighlight %}

<script>
s=[m=0]
draw=_=>{
createCanvas(b=400,b).parent("p5sketch")
a=m++%255
colorMode(HSB)
e=b*sin(m/200)/2
s[m%80]=[e*cos(c=e/10+PI*(m%2))+200,e*sin(c)+200,m,0]
s['map'](_=>(_[3]+=.1,y=_[1]+=_[3],x=_[0]+sin(_[3])*20)+strokeWeight(9-_[3])+stroke(_[2]*2%b,b,b)+point(x,y))}//#つぶやきProcessing
</script>