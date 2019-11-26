---
layout: post
title:  "Starfield Alt"
author: naoto
categories: [ sketch ]
tags: [ p5js, danshiffman, remix ]
image: assets/images/2018-01-03-starfield-alt.png
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
// Code for: https://youtu.be/17WoOqgXsRM

// instance mode by Naoto Hieda

function Star(sketch) {
  this.x = sketch.random(-sketch.width, sketch.width)/2;
  this.y = sketch.random(-sketch.height, sketch.height)/2;
  this.z = sketch.random(sketch.width);
  this.pz = this.z;
  if(sketch.random(1) > 0.) {
    this.tail = 10;
  }
  else {
    this.tail = 1;
  }

  this.update = function(speed) {
    this.z = this.z - speed;
    if (this.z < 1) {
      this.z = sketch.width;
      this.x = sketch.random(-sketch.width, sketch.width)/2;
      this.y = sketch.random(-sketch.height, sketch.height)/2;
      this.pz = this.z;
    }
  }

  this.show = function() {
    var sx = sketch.map(this.x / this.z, 0, 1, 0, sketch.width);
    var sy = sketch.map(this.y / this.z, 0, 1, 0, sketch.height);

    for(var i = 0; i < this.tail; i++) {
      var pz = this.pz + i * 20;
      var px = sketch.map(this.x / (this.pz+i*10), 0, 1, 0, sketch.width);
      var py = sketch.map(this.y / (this.pz+i*10), 0, 1, 0, sketch.height);

      sketch.fill(255, sketch.map(i, 0, 10, 255, 0));
      sketch.noStroke();
      var r = sketch.map(pz, 0, sketch.width, 16, 0);
      sketch.ellipse(px, py, r, r);
      }

    // sketch.fill(255, 128);
    // sketch.noStroke();

    // var r = sketch.map(this.z, 0, sketch.width, 16, 0);
    // sketch.ellipse(sx, sy, r, r);

    this.pz = this.z;
  }
}

var s = function( sketch ) {

  var stars = [];

  var speed = 0;

  sketch.setup = function () {
    sketch.createCanvas(400, 400);
    for (var i = 0; i < 100; i++) {
      stars[i] = new Star(sketch);
    }
  }

  sketch.draw = function () {
    if(sketch.frameCount % 60 == 0) {
      if(sketch.frameCount % 120 < 60) {
        speed = sketch.random(10, 20);
      }
      else {
        speed = sketch.random(50, 100);
      }
    }
    sketch.background(0);
    sketch.translate(sketch.width / 2, sketch.height / 2);
    for (var i = 0; i < stars.length; i++) {
      stars[i].update(speed);
      stars[i].show();
    }
  };
};

var p001 = new p5(s, document.getElementById('p5sketch'));
</script>