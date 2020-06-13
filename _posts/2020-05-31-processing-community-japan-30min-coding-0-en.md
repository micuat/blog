---
layout: post
title:  "Processing Community Japan 30 Minute Coding 0"
author: naoto
categories: [ report ]
tags: [ essay ]
image: assets/images/2020-05-31-processing-community-japan-30min-coding-0-en.png
description: "a report"
featured: true
comments: true
---

On May 31st, 2020, the Processing Community Japan live-streamed a program called 30 Minute Coding. As it was a test run, the program was the 0th edition of our series. Prior to the event, [Takawo-san](https://twitter.com/takawo/) and I individually recorded a 30-minute session of programming on p5.js. We both coded from scratch based on the theme "Tsuyu" (a rainy season in Japan, usually around June). During the streaming, we both watched each other's coding (one at a time) and commented on how we code in Japanese. The intention of the streaming is to show the process of coding and to be open about challenges that we encounter however experienced we are. This idea is inspired by the first episode of Dan Shiffman's [Coding Challenge](https://www.youtube.com/watch?v=17WoOqgXsRM), which had a 10-minute limit but later he changed the concept to code without time limitation.

I started by making a weather map because Tsuyu is represented by an iconic front in a map. I created a vector field which resembles a wind map, but it was far from a contour map. I quickly noticed that generating contours might be a challenging task within the given time. Instead, I made a simple drawing system so that the user can hand-draw an island (e.g., the main island of Japan). The vectors that lie on top of the island are programmed to rotate; however, I made a mistake on addressing pixels, which took me extra 10 minutes (not show in the video) to debug.

My code can be found here: [https://editor.p5js.org/micuat/sketches/fK7dxcq-d](https://editor.p5js.org/micuat/sketches/fK7dxcq-d)

Takawo-san took a completely different (and more straightforward) approach to create a sketch of rain. Unlike the Typhoon season, light rain lasts long during the Tsuyu season. As he is a master of texture generation and layering in p5.js, Takawo-san took his regular approach to create layers of waves. Also he used [`CanvasRenderingContext2D`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) known as `drawingContext` in p5.js to create glow effects.

Takawo-san's code can be found here: [https://www.openprocessing.org/sketch/907101](https://www.openprocessing.org/sketch/907101)

We are interested in making this as a series with contributions from people in the community. Since the 30-minute coding video can be silent, we can open a call to a global community and comment in Japanese to share ideas. Also, we might start an English version of the streaming. Let's get in touch if you are interested in joining the online activities about Processing and p5.js!

<iframe width="560" height="315" src="https://www.youtube.com/embed/h8MKyxiXLVI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>