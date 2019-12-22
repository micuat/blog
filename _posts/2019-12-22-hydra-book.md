---
layout: post
title:  "Hydra Book"
author: naoto
categories: [ sketch ]
tags: [ hydra, webgl ]
image: assets/images/2019-12-22-hydra-book-osckaleid2.png
description: "A sketch"
featured: true
comments: true
---

Preface
========

[Hydra](https://hydra-editor.glitch.me/) is an analog-synth-like coding environment for real-time visuals. It is created by Olivia Jack and is [open-source](https://github.com/ojack/hydra); thus, you can either open the browser version or clone the repository to serve it on your computer. There are a few resources:

* [The official documentation](https://github.com/ojack/hydra#Getting-Started) is a good resource to get started,
* [Function list](https://github.com/ojack/hydra/blob/master/docs/funcs.md) covers all the functions available in Hydra,
* and [Hydra Patterns on Twitter](https://twitter.com/hydra_patterns) is a way to get inspirations from other artists.

This article is a work-in-progress online book to collect Hydra snippets. The goal is not only to accumulate frequently-used techniques to make coding easier but also to research the theory of Hydra to discover new images.


Textures
========

In this chapter, we discuss textures or patterns, separately from colors or movements. Most of the snippets have low saturation and no movements in order to separate textures from other effects.

Oscillator
--------

`osc()` is one of the basic sources to create a texture. The first argument determines the frequency (i.e., how packed the stripes are), the second for the sync (i.e., the scroll speed), and the third for the offset, which adds color to the pattern.

{% highlight javascript %}
osc(40,0).out(o0)
{% endhighlight %}

![osc]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-osc.png)

By adding `thresh()` or `posterize()`, the oscillator pattern becomes clear stripes. `pixelate()` can achieve a similar effect; however, with sync parameter, the movement will appear differently.

{% highlight javascript %}
osc(40,0).thresh().out(o0)
{% endhighlight %}

![osc-thresh]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-oscthresh.png)

`kaleid()` with a large number creates circles,

{% highlight javascript %}
osc(200, 0).kaleid(200).out(o0)
{% endhighlight %}

![osc-kaleid]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-osckaleid.png)

and with a small number it becomes geometric shapes (in the example, an oscillator is combined with `kaleid` and `thresh`).

{% highlight javascript %}
osc(40,0).thresh().kaleid(3).out(o0)
{% endhighlight %}

![osc-kaleid2]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-osckaleid2.png)

Noise
--------

`noise()` is another basic function as a source. A texture is generated based on a variant of Perlin Noise.

{% highlight javascript %}
noise(10, 0).out(o0)
{% endhighlight %}

![noise]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-noise.png)

We will look more into detail in the modulator section.

Voronoi
--------

`voronoi()` is a source to generate a Voronoi diagram.

{% highlight javascript %}
voronoi(10, 0).out(o0)
{% endhighlight %}

![voronoi]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-voronoi.png)

Shapes
--------

`shape()` generates a polygon with a number of sides set by the first argument. Nevertheless, it is more than just a polygon - the second argument changes the size of the shape, and most importantly, the third argument can set gradient of the shape. For example, `shape(2)` is a thick line, which can be scaled to make a thin line.

{% highlight javascript %}
shape(2).scale(0.01).out(o0)
{% endhighlight %}

![line]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-line.png)

By repeating `shape(4)` and overlapping them, it gives a grid-like pattern. For convenience, a parameter and a function are stored in JavaScript variables.

{% highlight javascript %}
n = 4
a = () => shape(4,0.4).repeat(n,n)
a().add(a().scrollX(0.5/n).scrollY(0.5/n),1).out()
{% endhighlight %}

![shapes]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-shapes.png)

Similar to `kaleid()`, `shape()` with a large number of sides creates a circle. By tweaking the example above, it generates a Polka dot pattern.

{% highlight javascript %}
n = 4
a = () => shape(400,0.5).repeat(n,n)
a().add(a().scrollX(0.5/n).scrollY(0.5/n),1).out()
{% endhighlight %}

or almost equivalent with (the center of the image will be horizontally shifted)

{% highlight javascript %}
n = 8/Math.sqrt(2)
a = () => shape(400,0.75).repeat(n,n)
a().rotate(Math.PI/4).out()
{% endhighlight %}

![polka]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-polka.png)

Modulator
--------

Modulators are the key component in Hydra. Let's look at this example:

The modulated function (top left):
{% highlight javascript %}
osc(40,0,1)
{% endhighlight %}

The modulating function (top right):
{% highlight javascript %}
noise(3,0)
{% endhighlight %}

The result (bottom)
{% highlight javascript %}
osc(40,0,1).modulate(noise(3,0))
{% endhighlight %}

![oscmod]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-oscmod.png)

We can make a few observations. First, the color of the original image (or modulated image, `osc(40,0,1)`) is preserved. Second, the oscillator is distorted to resemble the pattern of the modulating texture, `noise(3,0)`. Modulators can be seen from two different perspectives. On the one hand, a modulator literally modulates (or distorts) the chained function (`osc` in this example). In this section, we cover this aspect to explore the distortion. On the other hand, it can be seen as a way to paint the modulator function (`noise` in this example). For example, `noise` itself is grayscale, but using it as an argument of a modulator, the noise pattern is painted with, for example, an oscillator or a gradient.

A modulator with a feedback loop keeps *pushing* pixels based on its brightness. In this example, a noise is modulated by itself in a feedback loop. As a result, bright pixels are pushed further and further, creating a smooth, 3D-like effect.

{% highlight javascript %}
noise(10, 0).modulate(o0).blend(o0,0.9).out(o0)
{% endhighlight %}

![noise-mod]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-noisemod.png)

This example uses the same technique on a Voronoi diagram. Similar to above, the resulting image has a fake 3D look.

{% highlight javascript %}
voronoi(10, 0).modulate(o0).blend(o0,0.9).out(o0)
{% endhighlight %}

![voronoi-mod]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-voronoimod.png)

Modulators can be chained to create complex patterns. In the examples above, pixels are pushed based on their brightness but always to the same directions. By normalizing an image from `[0, 1]` to, for example, `[-1, 1]`, pixels are pushed to two opposite directions. This can be achieved by `color(2,2).add(solid(-1,-1))` (notice that only red and green are selected because blue channel is ignored by a modulator).

{% highlight javascript %}
noise(5,0.0).shift(0.5).modulate(o1,0.1).modulate(src(o1).color(10,10).add(solid(-14,-14)),0.005).blend(o1,0.7).out(o1)
src(o1).shift(0.5).saturate(0).out(o0)
{% endhighlight %}

![noise-mod2]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-noisemod2.png)

The same technique can be applied to another texture. In this example, a square grid is used, but the second and third arguments of `shape()` is changed to add gradient, which helps modulating an image.

{% highlight javascript %}
n = 3
a = () => shape(4,0.2,0.9).repeat(n,n)
a().add(a().scrollX(0.5/n).scrollY(0.5/n),1).shift(0.5).modulate(o1,0.1).modulate(src(o1).color(10,10).add(solid(-14,-14)),0.005).blend(o1,0.7).out(o1)
src(o1).shift(0.5).saturate(0).out(o0)
{% endhighlight %}

![shapes-mod]({{ site.baseurl }}/assets/images/2019-12-22-hydra-book-shapesmod.png)

Colors
========

Motions
========