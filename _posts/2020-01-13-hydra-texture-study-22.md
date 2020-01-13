---
layout: post
title:  "Hydra Texture Study 22"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-13-hydra-texture-study-22.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=ZocNldsWJVKE1dBO&code=REQlM0QwLjAxJTNCYiUzRChvJTJDdSUyQ2klMkN5JTJDeiklM0QlM0VvKCkuYWRkKHNvbGlkKDElMkMxJTJDMSklMkNERCkudGhyZXNoKGkqMC4yKih6LXkpJTJCeSUyQzApLmx1bWEoMC41JTJDMCkuY29sb3IoYyh1JTJDaSUyQzApJTJDYyh1JTJDaSUyQzEpJTJDYyh1JTJDaSUyQzIpKSUzQmMlM0QodSUyQ2klMkNqKSUzRCUzRSU3QmxldCUyMGNjJTIwJTNEJTIwdS5zcGxpdCglMjIlMkYlMjIpJTJDJTIwY3MlMjAlM0QlMjBjYyU1QmNjLmxlbmd0aCUyMC0lMjAxJTVELnNwbGl0KCUyMi0lMjIpJTNCcmV0dXJuJTIwcGFyc2VJbnQoJTIyMHglMjIlMjAlMkIlMjBjcyU1QmklNUQuc3Vic3RyaW5nKDIqaiUyQyUyMDIlMkIyKmopKSUyRjI1NSUzQiU3RCUzQmNvbG9yaXplJTNEKHglMkN1JTJDeSUzRDAlMkN6JTNEMSklM0QlM0ViKHglMkN1JTJDMCUyQ3klMkN6KS5sYXllcihiKHglMkN1JTJDMSUyQ3klMkN6KSkubGF5ZXIoYih4JTJDdSUyQzIlMkN5JTJDeikpLmxheWVyKGIoeCUyQ3UlMkMzJTJDeSUyQ3opKS5sYXllcihiKHglMkN1JTJDNCUyQ3klMkN6KSklMEElMEF1cmwlM0QnaHR0cHMlM0ElMkYlMkZjb29sb3JzLmNvJTJGYmJkZWYwLWYwODcwMC1mNDlmMGEtZWZjYTA4LTAwYTZhNiclMEF5JTNEKCklM0QlM0VzaGFwZSg0JTJDMC4zJTJDMSkucmVwZWF0KDIwJTJDMjApLnNjcm9sbFgoLjElMkMuMSklMEF6JTNEKHQlM0QwLjc1KSUzRCUzRW9zYygzMS40JTJDMCkubWFzayhzaGFwZSgyJTJDMC4xJTJDMCkuc2Nyb2xsWSgxJTJDMSkpLm1vZHVsYXRlU2NhbGUob3NjKDEwJTJDMC4xKS5yb3RhdGUoMSklMkMuNCkucm90YXRlKC45JTJDLjEpLm1vZHVsYXRlUm90YXRlKG9zYyhNYXRoLlBJJTJDMSkucGl4ZWxhdGUoMSUyQzEpLnRocmVzaCguNSUyQy4wKSkubHVtYSh0JTJDMC4wKSUwQXNyYyhvMCkuaHVlKC0uMDAzKS5jb2xvciguOTklMkMuOTklMkMuOTkpLmxheWVyKHooMC4xKS5sdW1hKDAuMyUyQzAuMikuY29sb3IoMCUyQzAlMkMwJTJDMC40KSkubGF5ZXIoY29sb3JpemUoeiUyQ3VybCUyQzAuNzUlMkMxKS5sdW1hKDAuMSUyQzAuMCkpLm91dCgpJTBB)

{% highlight javascript %}
DD=0.01;b=(o,u,i,y,z)=>o().add(solid(1,1,1),DD).thresh(i*0.2*(z-y)+y,0).luma(0.5,0).color(c(u,i,0),c(u,i,1),c(u,i,2));c=(u,i,j)=>{let cc = u.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;};colorize=(x,u,y=0,z=1)=>b(x,u,0,y,z).layer(b(x,u,1,y,z)).layer(b(x,u,2,y,z)).layer(b(x,u,3,y,z)).layer(b(x,u,4,y,z))

url='https://coolors.co/bbdef0-f08700-f49f0a-efca08-00a6a6'
y=()=>shape(4,0.3,1).repeat(20,20).scrollX(.1,.1)
z=(t=0.75)=>osc(31.4,0).mask(shape(2,0.1,0).scrollY(1,1)).modulateScale(osc(10,0.1).rotate(1),.4).rotate(.9,.1).modulateRotate(osc(Math.PI,1).pixelate(1,1).thresh(.5,.0)).luma(t,0.0)
src(o0).hue(-.003).color(.99,.99,.99).layer(z(0.1).luma(0.3,0.2).color(0,0,0,0.4)).layer(colorize(z,url,0.75,1).luma(0.1,0.0)).out()
{% endhighlight %}
