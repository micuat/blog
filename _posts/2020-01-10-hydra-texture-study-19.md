---
layout: post
title:  "Hydra Texture Study 19"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-10-hydra-texture-study-19.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=uMxcrTYEZU1h1Q2x&code=YiUzRChvJTJDdDElMkN0MiklM0QlM0VzcmMobykuYWRkKHNvbGlkKDElMkMxJTJDMSklMkMwLjAxKS5sdW1hKHQxJTJDMC4wKS5tdWx0KHNyYyhvKS5hZGQoc29saWQoMSUyQzElMkMxKSUyQy0wLjAxKS5pbnZlcnQoKS5sdW1hKDEtdDIlMkMwLjApKS50aHJlc2goMC4wMDAxJTJDMC4wKSUwQWMlM0QoaSUyQ2opJTNEJTNFJTdCbGV0JTIwY2MlMjAlM0QlMjAnaHR0cHMlM0ElMkYlMkZjb29sb3JzLmNvJTJGZjNmZmJkLTI0N2JhMC03MGMxYjMtYjJkYmJmLWZmMTY1NCcuc3BsaXQoJTIyJTJGJTIyKSUyQyUyMGNzJTIwJTNEJTIwY2MlNUJjYy5sZW5ndGglMjAtJTIwMSU1RC5zcGxpdCglMjItJTIyKSUzQnJldHVybiUyMHBhcnNlSW50KCUyMjB4JTIyJTIwJTJCJTIwY3MlNUJpJTVELnN1YnN0cmluZygyKmolMkMlMjAyJTJCMipqKSklMkYyNTUlM0IlN0QlMEFkJTNEKG8lMkNpKSUzRCUzRWIobyUyQ2kqMC4yJTJDKGklMkIxKSowLjIpLmNvbG9yKGMoaSUyQzApJTJDYyhpJTJDMSklMkNjKGklMkMyKSklMEFkKG8xJTJDMCkubGF5ZXIoZChvMSUyQzEpKS5sYXllcihkKG8xJTJDMikpLmxheWVyKGQobzElMkMzKSkubGF5ZXIoZChvMSUyQzQpKS5vdXQobzIpJTBBeCUzRCgpJTNEJTNFb3NjKDQwJTJDMC4xJTJDMCkubW9kdWxhdGUobm9pc2UoMTAlMkMwLjEpJTJDMC4xKSUwQXgoKS5vdXQobzEpJTBBc3JjKG8yKS5rYWxlaWQoNikubW9kdWxhdGVSb3RhdGUob3NjKDMlMkMwLjIpLmthbGVpZCgxMDApJTJDMikub3V0KG8wKQ==)

{% highlight javascript %}
b=(o,t1,t2)=>src(o).add(solid(1,1,1),0.01).luma(t1,0.0).mult(src(o).add(solid(1,1,1),-0.01).invert().luma(1-t2,0.0)).thresh(0.0001,0.0)
c=(i,j)=>{let cc = 'https://coolors.co/f3ffbd-247ba0-70c1b3-b2dbbf-ff1654'.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;}
d=(o,i)=>b(o,i*0.2,(i+1)*0.2).color(c(i,0),c(i,1),c(i,2))
d(o1,0).layer(d(o1,1)).layer(d(o1,2)).layer(d(o1,3)).layer(d(o1,4)).out(o2)
x=()=>osc(40,0.1,0).modulate(noise(10,0.1),0.1)
x().out(o1)
src(o2).kaleid(6).modulateRotate(osc(3,0.2).kaleid(100),2).out(o0)
{% endhighlight %}
