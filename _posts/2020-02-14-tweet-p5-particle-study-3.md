---
layout: post
title:  "Tweet P5 Particle Study 3"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-14-tweet-p5-particle-study-3.png
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
setup=_=>createCanvas(b=400,b)
draw=_=>{
background(0,30);
a=m++%255
d=200
e=99*sin(m/d)
s[m%b]={c:e/10,e,w:0,a}
s['map'](_=>(_.w+=1/b,_.c+=_.w,_.e+=_.c/d)+strokeWeight(5-_.w*3)+stroke(0,_.a,d)+point(_.e*cos(_.c)+d,_.e*sin(_.c)+99+_.c))}//#つぶやきProcessing
{% endhighlight %}

<script>
s=[m=0]
setup=_=>createCanvas(b=400,b).parent("p5sketch")
draw=_=>{
background(0,30);
a=m++%255
d=200
e=99*sin(m/d)
s[m%b]={c:e/10,e,w:0,a}
s['map'](_=>(_.w+=1/b,_.c+=_.w,_.e+=_.c/d)+strokeWeight(5-_.w*3)+stroke(0,_.a,d)+point(_.e*cos(_.c)+d,_.e*sin(_.c)+99+_.c))}//#つぶやきProcessing
</script>