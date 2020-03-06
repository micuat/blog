---
layout: post
title:  "Tweet P5 Geometry Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-03-06-tweet-p5-geometry-study-1.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
setup=r=>createCanvas(W=400,W),t=0,draw=n=>{background(W),noFill(),r=200,s=99*sin(t+=.1),u=mouseX-r,v=mouseY-r,e=u*u+v*v,a=(e+r*r-s*s)/2,q=sqrt(e*r*r-a*a),translate(r,r),o=circle,o((a*u+v*q)/e,(a*v-u*q)/e,9),o((a*u-v*q)/e,(a*v+u*q)/e,9),o(0,0,W),o(u,v,2*s)};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
setup=r=>createCanvas(W=400,W).parent('p5sketch'),t=0,draw=n=>{background(W),noFill(),r=200,s=99*sin(t+=.1),u=mouseX-r,v=mouseY-r,e=u*u+v*v,a=(e+r*r-s*s)/2,q=sqrt(e*r*r-a*a),translate(r,r),o=circle,o((a*u+v*q)/e,(a*v-u*q)/e,9),o((a*u-v*q)/e,(a*v+u*q)/e,9),o(0,0,W),o(u,v,2*s)};//#つぶやきProcessing
</script>
