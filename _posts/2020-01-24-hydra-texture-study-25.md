---
layout: post
title:  "Hydra Texture Study 25"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-24-hydra-texture-study-25.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=qX93EVqR9cawh7pW&code=REQlM0QwLjAxJTNCYiUzRChvJTJDdSUyQ2klMkN5JTJDeiklM0QlM0VvKCkuYWRkKHNvbGlkKDElMkMxJTJDMSklMkNERCkudGhyZXNoKGkqMC4yKih6LXkpJTJCeSUyQzApLmx1bWEoMC41JTJDMCkuY29sb3IoYyh1JTJDaSUyQzApJTJDYyh1JTJDaSUyQzEpJTJDYyh1JTJDaSUyQzIpKSUzQmMlM0QodSUyQ2klMkNqKSUzRCUzRSU3QmxldCUyMGNjJTIwJTNEJTIwdS5zcGxpdCglMjIlMkYlMjIpJTJDJTIwY3MlMjAlM0QlMjBjYyU1QmNjLmxlbmd0aCUyMC0lMjAxJTVELnNwbGl0KCUyMi0lMjIpJTNCcmV0dXJuJTIwcGFyc2VJbnQoJTIyMHglMjIlMjAlMkIlMjBjcyU1QmklNUQuc3Vic3RyaW5nKDIqaiUyQyUyMDIlMkIyKmopKSUyRjI1NSUzQiU3RCUzQmNvbG9yaXplJTNEKHglMkN1JTJDeSUzRDAlMkN6JTNEMSklM0QlM0ViKHglMkN1JTJDMCUyQ3klMkN6KS5sYXllcihiKHglMkN1JTJDMSUyQ3klMkN6KSkubGF5ZXIoYih4JTJDdSUyQzIlMkN5JTJDeikpLmxheWVyKGIoeCUyQ3UlMkMzJTJDeSUyQ3opKS5sYXllcihiKHglMkN1JTJDNCUyQ3klMkN6KSklMEElMEF1cmwlM0QnZWNjYmQ5LWUxZWZmNi05N2QyZmItODNiY2ZmLTgwZmZlOCclMEF6JTNEKCklM0QlM0Vvc2MoMiUyQy4zKS5jb250cmFzdCgwLjcpJTBBc3JjKG8xKS5tb2R1bGF0ZVJvdGF0ZShzcmMobzEpLmh1ZSgwLjUpLmFkZChncmFkaWVudCgxLjUpLnNhdHVyYXRlKDMpLmh1ZSgwLjMpJTJDLjQpJTJDLjAxJTJDLjAxKS5tb2R1bGF0ZVNjYWxlKG9zYygxJTJDMSkucGl4ZWxhdGUoMSUyQzEpLnNhdHVyYXRlKDApJTJDMC4wMSUyQzEpLmxheWVyKGNvbG9yaXplKHolMkN1cmwpLm1hc2soc2hhcGUoMiUyQzAuMDUlMkMwLjApKS5tb2R1bGF0ZShvc2MoMSUyQzEpJTJDMSkpLm91dChvMSklMEFzb2xpZCgpLmxheWVyKG8xKS5vdXQobzAp)

{% highlight javascript %}
DD=0.01;b=(o,u,i,y,z)=>o().add(solid(1,1,1),DD).thresh(i*0.2*(z-y)+y,0).luma(0.5,0).color(c(u,i,0),c(u,i,1),c(u,i,2));c=(u,i,j)=>{let cc = u.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;};colorize=(x,u,y=0,z=1)=>b(x,u,0,y,z).layer(b(x,u,1,y,z)).layer(b(x,u,2,y,z)).layer(b(x,u,3,y,z)).layer(b(x,u,4,y,z))

url='eccbd9-e1eff6-97d2fb-83bcff-80ffe8'
z=()=>osc(2,.3).contrast(0.7)
src(o1).modulateRotate(src(o1).hue(0.5).add(gradient(1.5).saturate(3).hue(0.3),.4),.01,.01).modulateScale(osc(1,1).pixelate(1,1).saturate(0),0.01,1).layer(colorize(z,url).mask(shape(2,0.05,0.0)).modulate(osc(1,1),1)).out(o1)
solid().layer(o1).out(o0)
{% endhighlight %}
