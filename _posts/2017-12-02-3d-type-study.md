---
layout: post
title:  "3D Type Study"
author: naoto
categories: [ sketch ]
image: assets/images/2017-12-02-3D-Type-Study.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<script>
//.parent("p5sketch")
let font;

function preload() {
  font = loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');
}

let textTyped = 'december';
let points, bounds;

function setup() {
  createCanvas(400, 400, WEBGL).parent("p5sketch");
  if (textTyped.length > 0) {
    // get the points on font outline
    points = font.textToPoints(textTyped, 0, 0, 100, {
      sampleFactor: 0.1,
    });
    bounds = font.textBounds(textTyped, 0, 0, 100);
  }
}

function draw() {
  background(0);
  // margin border
  rotateY(millis() * 0.001);
  translate(-bounds.w * 0.5, bounds.h * 0.5);

  let l = 50;
  // lines
  stroke(255, 100);
  strokeWeight(1.0);
  let xx = map(millis() * 0.001 * 0.5 % 1, 0, 1, -500, 500);
  for (let i = 0; i < points.length - 1; i++) {
    if (xx - 100 < points[i].x && points[i].x < xx)
      line(points[i].x, points[i].y, -l, points[i].x, points[i].y, l);
  }

  // dots
  fill(255, 100);
  noStroke();
  for (let i = 0; i < points.length; i++) {
    if (xx - 100 < points[i].x && points[i].x < xx) continue;
    let diameter = 3;
    // on ervery second point
    push();
    translate(0, 0, -l);
    ellipse(points[i].x, points[i].y, diameter, diameter);
    pop();

    push();
    translate(0, 0, l);
    ellipse(points[i].x, points[i].y, diameter, diameter);
    pop();
  }
}
</script>