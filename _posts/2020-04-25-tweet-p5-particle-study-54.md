---
layout: post
title:  "Tweet P5 Particle Study 54"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-04-25-tweet-p5-particle-study-54.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
t=[],N=0,draw=a=>{N||createCanvas(W=400,W),background(0,9),t[N++%200]=[f=(r=random)(W),g=r(W),W-f,W,f,g],t.find(a=>{t.find(([,,,,n,r])=>{m=mag(x=a[4]-n,y=a[5]-r)+1,m<30&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m),v=a[5]+=sin(B),stroke(a),line(u,v,n,r))})})};//#つぶやきProcessing
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
t=[],N=0,draw=a=>{N||createCanvas(W=400,W),background(0,9),t[N++%200]=[f=(r=random)(W),g=r(W),W-f,W,f,g],t.find(a=>{t.find(([,,,,n,r])=>{m=mag(x=a[4]-n,y=a[5]-r)+1,m<30&&(u=a[4]+=cos(B=a[1]+=atan2(x,y)/m),v=a[5]+=sin(B),stroke(a),line(u,v,n,r))})})};//#つぶやきProcessing
handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>
