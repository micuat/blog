---
layout: post
title:  "Hydra Texture Study 18"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-07-hydra-texture-study-18.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=CfiKbuZuZq2cIIRz&code=YiUzRChvJTJDdDElMkN0MiklM0QlM0VzcmMobykuYWRkKHNvbGlkKDElMkMxJTJDMSklMkMwLjAxKS5sdW1hKHQxJTJDMC4wKS5tdWx0KHNyYyhvKS5hZGQoc29saWQoMSUyQzElMkMxKSUyQy0wLjAxKS5pbnZlcnQoKS5sdW1hKDEtdDIlMkMwLjApKS50aHJlc2goMC4wMDAxJTJDMC4wKSUwQWMlM0QoaSUyQ2opJTNEJTNFJTdCbGV0JTIwY2MlMjAlM0QlMjAnaHR0cHMlM0ElMkYlMkZjb29sb3JzLmNvJTJGYjU0NDZlLTUyNTI1Mi1iYWRlZmMtMGFmZmVkLTlkNDRiNScuc3BsaXQoJTIyJTJGJTIyKSUyQyUyMGNzJTIwJTNEJTIwY2MlNUJjYy5sZW5ndGglMjAtJTIwMSU1RC5zcGxpdCglMjItJTIyKSUzQnJldHVybiUyMHBhcnNlSW50KCUyMjB4JTIyJTIwJTJCJTIwY3MlNUJpJTVELnN1YnN0cmluZygyKmolMkMlMjAyJTJCMipqKSklMkYyNTUlM0IlN0QlMEFiKG8xJTJDMC4wJTJDMC4yKS5jb2xvcihjKDAlMkMwKSUyQ2MoMCUyQzEpJTJDYygwJTJDMikpLmxheWVyKGIobzElMkMwLjIlMkMwLjgpLmNvbG9yKGMoMSUyQzApJTJDYygxJTJDMSklMkNjKDElMkMyKSkpLmxheWVyKGIobzElMkMwLjglMkMxKS5jb2xvcihjKDIlMkMwKSUyQ2MoMiUyQzEpJTJDYygyJTJDMikpKS5vdXQobzIpJTBBeCUzRCgpJTNEJTNFb3NjKDQwJTJDMC4xJTJDMCkubW9kdWxhdGUobm9pc2UoMTAlMkMwLjEpKSUwQXgoKS5vdXQobzEpJTBBc3JjKG8yKS5rYWxlaWQoNDApLmxheWVyKHNyYyhvMikua2FsZWlkKDQwKS5kaWZmKHNyYyhvMikubW9kdWxhdGVLYWxlaWQob3NjKDMlMkMwLjUpLmNvbG9yKDAuMiklMkM0MCkpLmx1bWEoMC41JTJDMC4wKS5odWUoMC4xKSkub3V0KG8wKQ==)

{% highlight javascript %}
b=(o,t1,t2)=>src(o).add(solid(1,1,1),0.01).luma(t1,0.0).mult(src(o).add(solid(1,1,1),-0.01).invert().luma(1-t2,0.0)).thresh(0.0001,0.0)
c=(i,j)=>{let cc = 'https://coolors.co/b5446e-525252-badefc-0affed-9d44b5'.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;}
b(o1,0.0,0.2).color(c(0,0),c(0,1),c(0,2)).layer(b(o1,0.2,0.8).color(c(1,0),c(1,1),c(1,2))).layer(b(o1,0.8,1).color(c(2,0),c(2,1),c(2,2))).out(o2)
x=()=>osc(40,0.1,0).modulate(noise(10,0.1))
x().out(o1)
src(o2).kaleid(40).layer(src(o2).kaleid(40).diff(src(o2).modulateKaleid(osc(3,0.5).color(0.2),40)).luma(0.5,0.0).hue(0.1)).out(o0)
{% endhighlight %}
