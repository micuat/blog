---
layout: post
title:  "Tweet P5 Particle Study 38"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-04-tweet-p5-particle-study-38.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W);N<41;N++)t[N]=[W-9*N,0,9*N];noStroke(),t.find(a=>{t.find(t=>{a!=t&&([q,r,s]=t,m=mag(x=a[0]-q,y=a[2]-s),b=a[1]+=m<N?atan2(x,y)/m:0,u=a[0]+=cos(b)/40,v=a[2]+=sin(b)/40)}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],c=N=0,draw=a=>{if(!c++)for(createCanvas(W=400,W).parent('p5sketch');N<41;N++)t[N]=[W-9*N,0,9*N];noStroke(),t.find(a=>{t.find(t=>{a!=t&&([q,r,s]=t,m=mag(x=a[0]-q,y=a[2]-s),b=a[1]+=m<N?atan2(x,y)/m:0,u=a[0]+=cos(b)/40,v=a[2]+=sin(b)/40)}),fill(a),circle(u,v,2)})};//#つぶやきProcessing
</script>
