---
layout: post
title:  "KUU QR Code"
author: naoto
categories: [ sketch ]
image: assets/images/2018-03-18-KUU-QR-Code.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

A sketch for KUU project.

<script>
//.parent("p5sketch")
var w = 10;
var n = 3;

function setup() {
  createCanvas(n * w * 22 - w * 1, n * w * 22 - w * 1).parent("p5sketch");
  background(255);

  function drawMarker() {
    fill(0);
    rect(0, 0, 7 * w, 7 * w);
    fill(255);
    rect(w, w, 5 * w, 5 * w);
    fill(0);
    rect(2 * w, 2 * w, 3 * w, 3 * w);
  }
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      push();
      translate(j * 22 * w, i * 22 * w);
      drawMarker();
      push();
      translate(14 * w, 0);
      drawMarker();
      pop();
      push();
      translate(0, 14 * w);
      drawMarker();
      pop();
      pop();
    }
  }
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      push();
      translate(j * 22 * w, i * 22 * w);
      drawQr(0);
      pop();
    }
  }
}

function draw() {
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      push();
      translate(j * 22 * w, i * 22 * w);
      drawQr(0.999);
      pop();
    }
  }
}

function drawQr(p) {
  noStroke();
  fill(0);
  for (let i = 0; i < 21; i++) {
    for (let j = 0; j < 21; j++) {
      if (i < 8 && j < 8) continue;
      if (i < 8 && j > 12) continue;
      if (i > 12 && j < 8) continue;
      if (random(1) < p) continue;
      if (random(1) < 0.5) {
        fill(0);
      } else {
        fill(255);
      }
      rect(j * w, i * w, w, w);
    }
  }
}
</script>