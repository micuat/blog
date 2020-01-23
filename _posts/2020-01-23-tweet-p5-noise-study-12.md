---
layout: post
title:  "Tweet P5 Noise Study 12"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-01-23-tweet-p5-noise-study-12.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

{% highlight javascript %}
setup=_=>createCanvas(w=200,w)+pixelDensity(1)+background(9,a=15e4)+rect(0,50,50,d=99)
draw=_=>{s='copyWithin'
loadPixels()
p=pixels
for(x=3;x<15e4;x+=9+p[a])p[s](x-w-p[a]*2,x+p[a],x+w)[x]=x%w<d?0:w;
p[s](a,1,2e3)[a-1]>d&&p[['reverse',s][p[8e4]%2]]()
updatePixels()}//#つぶやきprocessing
{% endhighlight %}

<script>
setup=_=>createCanvas(w=200,w).parent("p5sketch")+pixelDensity(1)+background(9,a=15e4)+rect(0,50,50,d=99)
draw=_=>{s='copyWithin'
loadPixels()
p=pixels
for(x=3;x<15e4;x+=9+p[a])p[s](x-w-p[a]*2,x+p[a],x+w)[x]=x%w<d?0:w;
p[s](a,1,2e3)[a-1]>d&&p[['reverse',s][p[8e4]%2]]()
updatePixels()}//#つぶやきprocessing
</script>