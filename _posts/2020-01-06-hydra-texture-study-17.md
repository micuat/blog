---
layout: post
title:  "Hydra Texture Study 17"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2020-01-06-hydra-texture-study-17.png
description: "A sketch"
featured: true
comments: true
---

[Sketch](https://hydra-editor.glitch.me/?sketch_id=8LYIrAdWDowpC5bE&code=b3NjKDQwJTJDMC4xJTJDMCkubW9kdWxhdGVTY2FsZShvc2MoNCUyQzElMkMwKSUyQzEpLm1vZHVsYXRlKG5vaXNlKDIlMkMwLjEpJTJDMC4xKS5tb2R1bGF0ZVBpeGVsYXRlKHNyYyhvMSkubHVtYSgwLjMlMkMwLjApJTJDJTIwMzAlMkMlMjA0KS5vdXQobzEpJTBBJTBBYiUzRChvJTJDdDElMkN0MiklM0QlM0VzcmMobykuYWRkKHNvbGlkKDElMkMxJTJDMSklMkMwLjAxKS5sdW1hKHQxJTJDMC4wKS5tdWx0KHNyYyhvKS5hZGQoc29saWQoMSUyQzElMkMxKSUyQy0wLjAxKS5pbnZlcnQoKS5sdW1hKDEtdDIlMkMwLjApKS50aHJlc2goMC4wMDAxJTJDMC4wKSUwQSUwQWMlM0QoaSUyQ2opJTNEJTNFJTdCJTBBJTIwJTIwbGV0JTIwdXJsJTNEJ2h0dHBzJTNBJTJGJTJGY29vbG9ycy5jbyUyRmY2NTExZC1mZmI0MDAtMDBhNmVkLTdmYjgwMC0wZDJjNTQnJTBBJTIwJTIwbGV0JTIwY2MlMjAlM0QlMjB1cmwuc3BsaXQoJTIyJTJGJTIyKSUzQiUwQSUyMCUyMGxldCUyMGNzJTIwJTNEJTIwY2MlNUJjYy5sZW5ndGglMjAtJTIwMSU1RC5zcGxpdCglMjItJTIyKSUzQiUwQSUyMCUyMHJldHVybiUyMHBhcnNlSW50KCUyMjB4JTIyJTIwJTJCJTIwY3MlNUJpJTVELnN1YnN0cmluZygyKmolMkMlMjAyJTJCMipqKSklMkYyNTUlM0IlMEElN0QlMEElMEFiKG8xJTJDMC4wJTJDMC4yKS5jb2xvcihjKDAlMkMwKSUyQ2MoMCUyQzEpJTJDYygwJTJDMikpLmxheWVyKGIobzElMkMwLjIlMkMwLjgpLmNvbG9yKGMoMSUyQzApJTJDYygxJTJDMSklMkNjKDElMkMyKSkpLmxheWVyKGIobzElMkMwLjglMkMxKS5jb2xvcihjKDIlMkMwKSUyQ2MoMiUyQzEpJTJDYygyJTJDMikpKS5vdXQobzIpJTBBJTBBJTBBc3JjKG8yKS5tb2R1bGF0ZShzcmMobzApLmNvbG9yKDElMkMxJTJDMCkuc2F0dXJhdGUoMCklMkMwLjQpLmJsZW5kKG8wJTJDMC43KS5vdXQobzAp)

{% highlight javascript %}
osc(40,0.1,0).modulateScale(osc(4,1,0),1).modulate(noise(2,0.1),0.1).modulatePixelate(src(o1).luma(0.3,0.0), 30, 4).out(o1)
b=(o,t1,t2)=>src(o).add(solid(1,1,1),0.01).luma(t1,0.0).mult(src(o).add(solid(1,1,1),-0.01).invert().luma(1-t2,0.0)).thresh(0.0001,0.0)
c=(i,j)=>{
  let url='https://coolors.co/f6511d-ffb400-00a6ed-7fb800-0d2c54'
  let cc = url.split("/");
  let cs = cc[cc.length - 1].split("-");
  return parseInt("0x" + cs[i].substring(2*j, 2+2*j))/255;
}
b(o1,0.0,0.2).color(c(0,0),c(0,1),c(0,2)).layer(b(o1,0.2,0.8).color(c(1,0),c(1,1),c(1,2))).layer(b(o1,0.8,1).color(c(2,0),c(2,1),c(2,2))).out(o2)
src(o2).modulate(src(o0).color(1,1,0).saturate(0),0.4).blend(o0,0.7).out(o0)
{% endhighlight %}
