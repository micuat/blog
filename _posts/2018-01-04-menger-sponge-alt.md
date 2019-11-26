---
layout: post
title:  "Menger Sponge Alt"
author: naoto
categories: [ sketch ]
tags: [ p5js, danshiffman, remix ]
image: assets/images/2018-01-04-menger-sponge-alt.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
// Daniel Shiffman
// http://codingtra.in
// http://patreon.com/codingtrain
// Code for this video: https://youtu.be/LG8ZK-rRkXo

// instance mode by Naoto Hieda

function Box(sketch, x, y, z, r, generation) {
  this.pos = sketch.createVector(x, y, z);
  this.r = r;
  this.color = sketch.random(255);
  this.generation = generation;

  this.generate = function () {
    var boxes = [];
    if(this.generation < 2) {
      for (var x = -1; x < 2; x++) {
        for (var y = -1; y < 2; y++) {
          for (var z = -1; z < 2; z++) {
            var sum = sketch.abs(x) + sketch.abs(y) + sketch.abs(z);
            var newR = this.r / 3;
            if (sum > 1) {
              var b = new Box(sketch, this.pos.x + x * newR, this.pos.y + y * newR, this.pos.z + z * newR, newR, this.generation + 1);
              b.color = sketch.map(b.pos.mag(), 0, 200, 255, 0);
              boxes.push(b);
            }
          }
        }
      }
    }
    return boxes;
  }

  this.show = function () {
    sketch.push();
    sketch.translate(this.pos.x, this.pos.y, this.pos.z);
    // sketch.rotateY(sketch.millis() * 0.001);
    sketch.noStroke();
    // sketch.fill(255, 0, 255);
    sketch.fill(200);
    sketch.box(this.r - 3);
    sketch.pop();
  }
}

var s = function (sketch) {

  var a = 0;

  var sponge = [];

  sketch.setup = function () {
    sketch.pos = sketch.createVector(0, 0, 0);

    sketch.createCanvas(400, 400, sketch.WEBGL);

    // An array of Box objects
    // Star with one
    var b = new Box(sketch, 0, 0, 0, 100, 0);
    sponge.push(b);

    // sketch.mousePressed();
    // sketch.mousePressed();
    // sketch.mousePressed();
  }

  sketch.generate = function () {
    // Generate the next set of boxes
    var next = [];
    for (var i = 0; i < sponge.length; i++) {
      var b = sponge[i];
      var newBoxes = [];
      if(sketch.pos.z > b.pos.z && sketch.pos.dist(b.pos) < 50 * (5 - b.generation) ) {// if(sketch.random(1) > 0.9) {
        newBoxes = b.generate();
      }

      if(newBoxes.length > 0)
        next = next.concat(newBoxes);
      else
        next = next.concat(b);
    }
    sponge = next;
    isRunning = false;
  }

  sketch.draw = function () {
    sketch.smooth();
    sketch.lights();
    sketch.colorMode(sketch.RGB, 255);
    sketch.background(0);
    sketch.pos.z = sketch.map(sketch.sin(sketch.millis() * 0.00025), 1, -1, 0, 400);
    sketch.translate(0, 0, 300);
    sketch.translate(sketch.pos.x, sketch.pos.y, -sketch.pos.z);
    if(sketch.frameCount % 120 == 0) sketch.generate();
    // sketch.rotateX(a);
    // sketch.rotateY(a * 0.4);
    // sketch.rotateZ(a * 0.1);
    // Show what you've got!
    for (var i = 0; i < sponge.length; i++) {
      // sponge[i].color = this.pos.dist(sponge[i].pos);
      sponge[i].show();
    }
    a += 0.01;
  }
};

var myp5 = new p5(s, document.getElementById('p5sketch'));
</script>