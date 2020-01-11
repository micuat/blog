---
layout: post
title:  "Hydra Texture Study 20"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-11-hydra-texture-study-20.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=ZuU2GmOg71SaBjlR&code=YiUzRChvJTJDdDElMkN0MiklM0QlM0VzcmMobykuYWRkKHNvbGlkKDElMkMxJTJDMSklMkMwLjAxKS5sdW1hKHQxJTJDMC4wKS5tdWx0KHNyYyhvKS5hZGQoc29saWQoMSUyQzElMkMxKSUyQy0wLjAxKS5pbnZlcnQoKS5sdW1hKDEtdDIlMkMwLjApKS50aHJlc2goMC4wMDAxJTJDMC4wKSUwQWMlM0QoaSUyQ2opJTNEJTNFJTdCbGV0JTIwY2MlMjAlM0QlMjAnaHR0cHMlM0ElMkYlMkZjb29sb3JzLmNvJTJGNTM4NmU0LTdmYzI5Yi1iNWVmOGEtZDdmMTcxLTgxN2U5Zicuc3BsaXQoJTIyJTJGJTIyKSUyQyUyMGNzJTIwJTNEJTIwY2MlNUJjYy5sZW5ndGglMjAtJTIwMSU1RC5zcGxpdCglMjItJTIyKSUzQnJldHVybiUyMHBhcnNlSW50KCUyMjB4JTIyJTIwJTJCJTIwY3MlNUJpJTVELnN1YnN0cmluZygyKmolMkMlMjAyJTJCMipqKSklMkYyNTUlM0IlN0QlMEFkJTNEKG8lMkNpKSUzRCUzRWIobyUyQ2kqMC4yJTJDKGklMkIxKSowLjIpLmNvbG9yKGMoaSUyQzApJTJDYyhpJTJDMSklMkNjKGklMkMyKSklMEElMEF2b3Jvbm9pKDMlMkMwLjMlMkMwLjApLnJvdGF0ZSgwLjElMkMwLjEpLmx1bWEoKS5vdXQobzIpJTBBRCUzRDAuMDAzJTBBc3JjKG8xKS5jb2xvcig0JTJDNCUyQzQpLmFkZChzcmMobzEpLnNjcm9sbFgoRCklMkMxKS5hZGQoc3JjKG8xKS5zY3JvbGxYKC1EKSUyQzEpLmFkZChzcmMobzEpLnNjcm9sbFkoRCklMkMxKS5hZGQoc3JjKG8xKS5zY3JvbGxZKC1EKSUyQzEpLmNvbG9yKDElMkY4JTJDMSUyRjglMkMxJTJGOCkuYmxlbmQobzIlMkMlMjAwLjAxKS5vdXQobzEpJTBBc3JjKG8xKS5sdW1hKDAuMyUyQzAuMCkuYWRkKG8yJTJDMC41KS5vdXQobzMpJTBBZChvMyUyQzApLmxheWVyKGQobzMlMkMxKSkubGF5ZXIoZChvMyUyQzIpKS5sYXllcihkKG8zJTJDMykpLmxheWVyKGQobzMlMkM0KSkub3V0KCklMEE=)

{% highlight javascript %}
b=(o,t1,t2)=>src(o).add(solid(1,1,1),0.01).luma(t1,0.0).mult(src(o).add(solid(1,1,1),-0.01).invert().luma(1-t2,0.0)).thresh(0.0001,0.0)
c=(i,j)=>{let cc = 'https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f'.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;}
d=(o,i)=>b(o,i*0.2,(i+1)*0.2).color(c(i,0),c(i,1),c(i,2))

voronoi(3,0.3,0.0).rotate(0.1,0.1).luma().out(o2)
D=0.003
src(o1).color(4,4,4).add(src(o1).scrollX(D),1).add(src(o1).scrollX(-D),1).add(src(o1).scrollY(D),1).add(src(o1).scrollY(-D),1).color(1/8,1/8,1/8).blend(o2, 0.01).out(o1)
src(o1).luma(0.3,0.0).add(o2,0.5).out(o3)
d(o3,0).layer(d(o3,1)).layer(d(o3,2)).layer(d(o3,3)).layer(d(o3,4)).out()
{% endhighlight %}
