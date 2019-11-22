---
layout: post
title:  "Tiny Sketch: Blobs"
author: naoto
categories: [ sketch ]
image: assets/images/2019-03-09-Tiny-Sketch-Blobs.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

blobs in 200 char w/ spaces ; thanks to 2bit for revision.

{% highlight javascript %}
m=0
setup=_=>createCanvas(b=400,b,s=[R=random])
draw=_=>{translate(a=m++%314?a:R(b),0)
rotate(m/50)
colorMode(HSB)
s[m%50]=`d=m-${m}
fill(d*2,a/5,b)
circle(${R(b)},${R(-b)},30*cos(d/10))`
s.map(eval)}
{% endhighlight %}

<script>
m=0
setup=_=>createCanvas(b=400,b,s=[R=random]).parent("p5sketch")
draw=_=>{translate(a=m++%314?a:R(b),0)
rotate(m/50)
colorMode(HSB)
s[m%50]=`d=m-${m}
fill(d*2,a/5,b)
circle(${R(b)},${R(-b)},30*cos(d/10))`
s.map(eval)}
</script>