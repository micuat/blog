---
layout: post
title:  "Grid Study"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2017-08-30-Grid-Study.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Reproduction of one of my first Processing sketches back in 2009.

<script>
//.parent("p5sketch")
function setup() {
  createCanvas(400, 400).parent("p5sketch");
}
function draw() {
  background(0);
  for(let i = 0; i < 16; i++) {
    stroke(255);
    let x = int(map(i, 0, 16, 0, width));
    line(x, 0, x, height);
    line(0, x, width, x);
  }
}
</script>