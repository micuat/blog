---
layout: post
title:  "Tweet P5 Particle Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-02-12-tweet-p5-particle-study-1.png
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
noStroke()
colorMode(HSB)
e=b*sin(m/(get(99,99)[1]-99+.5))
s[m%64]=`d=m-${m}
fill(${m*2%b},b,b)
circle(${e*cos(c=m/10+random(2))}+200,${e*sin(c)}+200,sin(d/20)*${e})`
s['map'](eval)}//#つぶやきProcessing
{% endhighlight %}

<script>
s=[m=0]
draw=_=>{
createCanvas(b=400,b).parent("p5sketch")
a=m++%255
noStroke()
colorMode(HSB)
e=b*sin(m/(get(99,99)[1]-99+.5))
s[m%64]=`d=m-${m}
fill(${m*2%b},b,b)
circle(${e*cos(c=m/10+random(2))}+200,${e*sin(c)}+200,sin(d/20)*${e})`
s['map'](eval)}//#つぶやきProcessing
</script>