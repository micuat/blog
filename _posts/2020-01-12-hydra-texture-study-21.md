---
layout: post
title:  "Hydra Texture Study 21"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-12-hydra-texture-study-21.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=IdNK35Uyu9tweGEn&code=REQlM0QwLjAxJTNCYiUzRChvJTJDdSUyQ2klMkN5JTJDeiklM0QlM0VvKCkuYWRkKHNvbGlkKDElMkMxJTJDMSklMkNERCkudGhyZXNoKGkqMC4yKih6LXkpJTJCeSUyQzApLmx1bWEoMC41JTJDMCkuY29sb3IoYyh1JTJDaSUyQzApJTJDYyh1JTJDaSUyQzEpJTJDYyh1JTJDaSUyQzIpKSUzQmMlM0QodSUyQ2klMkNqKSUzRCUzRSU3QmxldCUyMGNjJTIwJTNEJTIwdS5zcGxpdCglMjIlMkYlMjIpJTJDJTIwY3MlMjAlM0QlMjBjYyU1QmNjLmxlbmd0aCUyMC0lMjAxJTVELnNwbGl0KCUyMi0lMjIpJTNCcmV0dXJuJTIwcGFyc2VJbnQoJTIyMHglMjIlMjAlMkIlMjBjcyU1QmklNUQuc3Vic3RyaW5nKDIqaiUyQyUyMDIlMkIyKmopKSUyRjI1NSUzQiU3RCUzQmNvbG9yaXplJTNEKHglMkN1JTJDeSUzRDAlMkN6JTNEMSklM0QlM0ViKHglMkN1JTJDMCUyQ3klMkN6KS5sYXllcihiKHglMkN1JTJDMSUyQ3klMkN6KSkubGF5ZXIoYih4JTJDdSUyQzIlMkN5JTJDeikpLmxheWVyKGIoeCUyQ3UlMkMzJTJDeSUyQ3opKS5sYXllcihiKHglMkN1JTJDNCUyQ3klMkN6KSklMEElMEFzaGFwZSgzJTJDMC40JTJDMC4wMSkucm90YXRlKDElMkMxKS5tb2R1bGF0ZVNjYWxlKG9zYygxJTJDMSkucGl4ZWxhdGUoMSUyQzAuNSklMkMxJTJDMC4yKS5jb250cmFzdCgxLjIpLm91dChvMiklMEFEJTNEMC4wMDMlMEFzcmMobzEpLmNvbG9yKDQlMkM0JTJDNCkuYWRkKHNyYyhvMSkuc2Nyb2xsWChEKSUyQzEpLmFkZChzcmMobzEpLnNjcm9sbFgoLUQpJTJDMSkuYWRkKHNyYyhvMSkuc2Nyb2xsWShEKSUyQzEpLmFkZChzcmMobzEpLnNjcm9sbFkoLUQpJTJDMSkuY29sb3IoMSUyRjglMkMxJTJGOCUyQzElMkY4KS5ibGVuZChvMiUyQyUyMDAuMDMpLm91dChvMSklMEFOJTNEOCUzQnglM0QoKSUzRCUzRXNyYyhvMSkucmVwZWF0KE4lMkNOKS5tdWx0KG9zYygzKk4lMkMwKS5yb3RhdGUoMSkucGl4ZWxhdGUoTiUyQ04pLnRocmVzaCgwLjUlMkMwKSkuYWRkKHNyYyhvMSkuc2NhbGUoMSUyQy0xJTJDMSkucmVwZWF0KE4lMkNOKS5tdWx0KG9zYygzKk4lMkMwKS5yb3RhdGUoMSkucGl4ZWxhdGUoTiUyQ04pLmludmVydCgpLnRocmVzaCgwLjUlMkMwKSkpJTBBeSUzRCgpJTNEJTNFc2hhcGUoNCUyQzAuNiUyQzAuMykuZGlmZihzaGFwZSg0JTJDMC4xJTJDMC43KSkubW9kdWxhdGVTY2FsZShvc2MoMSUyQzEpLmthbGVpZCgxMDApJTJDMS41JTJDMSkucm90YXRlKE1hdGguUEklMkY0KS5yZXBlYXQoOCUyQzgpLnRocmVzaCgwLjUlMkMwLjApJTBBeiUzRCgpJTNEJTNFb3NjKDUwKS5tb2R1bGF0ZShub2lzZSg0JTJDMC4xKSUyQzAuMDMpJTBBY29sb3JpemUoeCUyQycwNGU3NjItZjViNzAwLTAwYTFlNC1kYzAwNzMtODlmYzAwJykubGF5ZXIoY29sb3JpemUoeiUyQycwNGU3NjItZjViNzAwLTAwYTFlNC1kYzAwNzMtODlmYzAwJykubWFzayh5KCkpKS5vdXQoKSUwQQ==)

{% highlight javascript %}
DD=0.01;b=(o,u,i,y,z)=>o().add(solid(1,1,1),DD).thresh(i*0.2*(z-y)+y,0).luma(0.5,0).color(c(u,i,0),c(u,i,1),c(u,i,2));c=(u,i,j)=>{let cc = u.split("/"), cs = cc[cc.length - 1].split("-");return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;};colorize=(x,u,y=0,z=1)=>b(x,u,0,y,z).layer(b(x,u,1,y,z)).layer(b(x,u,2,y,z)).layer(b(x,u,3,y,z)).layer(b(x,u,4,y,z))

shape(3,0.4,0.01).rotate(1,1).modulateScale(osc(1,1).pixelate(1,0.5),1,0.2).contrast(1.2).out(o2)
D=0.003
src(o1).color(4,4,4).add(src(o1).scrollX(D),1).add(src(o1).scrollX(-D),1).add(src(o1).scrollY(D),1).add(src(o1).scrollY(-D),1).color(1/8,1/8,1/8).blend(o2, 0.03).out(o1)
N=8;x=()=>src(o1).repeat(N,N).mult(osc(3*N,0).rotate(1).pixelate(N,N).thresh(0.5,0)).add(src(o1).scale(1,-1,1).repeat(N,N).mult(osc(3*N,0).rotate(1).pixelate(N,N).invert().thresh(0.5,0)))
y=()=>shape(4,0.6,0.3).diff(shape(4,0.1,0.7)).modulateScale(osc(1,1).kaleid(100),1.5,1).rotate(Math.PI/4).repeat(8,8).thresh(0.5,0.0)
z=()=>osc(50).modulate(noise(4,0.1),0.03)
colorize(x,'04e762-f5b700-00a1e4-dc0073-89fc00').layer(colorize(z,'04e762-f5b700-00a1e4-dc0073-89fc00').mask(y())).out()
{% endhighlight %}
