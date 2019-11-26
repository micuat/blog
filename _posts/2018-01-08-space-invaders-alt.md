---
layout: post
title:  "Space Invaders Alt"
author: naoto
categories: [ sketch ]
tags: [ p5js, danshiffman, remix ]
image: assets/images/2018-01-08-space-invaders-alt.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Use left / right / space keys.

<script>
// Daniel Shiffman
// http://codingtra.in
// http://patreon.com/codingtrain
// Code for: https://youtu.be/biN3v3ef-Y0

// instance mode by Naoto Hieda

function Drop(sketch, x, y) {
  this.x = x;
  this.y = y;
  this.r = 8;
  this.toDelete = false;

  this.show = function() {
    sketch.stroke(255);
    sketch.strokeWeight(4);
    // sketch.ellipse(this.x, this.y, this.r*2, this.r*2);
    sketch.line(this.x, this.y, this.x, this.y + this.r*2);
  }

  this.evaporate = function() {
    this.toDelete = true;
  }

  this.hits = function(flower) {
    var d = sketch.dist(this.x, this.y, flower.x, flower.y);
    if (d < this.r + flower.r) {
      return true;
    } else {
      return false;
    }
  }

  this.move = function() {
    this.y = this.y - 5;
  }

}

function Flower(sketch, x, y, i) {
  this.dot1 = [
    [0, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0],
    [0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0],
    [0, 0, 1, 1, 1, 1, 1, 1, 1, 0, 0],
    [0, 1, 1, 0, 1, 1, 1, 0, 1, 1, 0],
    [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
    [1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1],
    [1, 0, 1, 0, 0, 0, 0, 0, 1, 0, 1],
    [0, 0, 0, 1, 1, 0, 1, 1, 0, 0, 0]
  ];

  this.dot2 = [
    [0, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0],
    [1, 0, 0, 1, 0, 0, 0, 1, 0, 0, 1],
    [1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1],
    [1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
    [0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0],
    [0, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0],
    [0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0]
  ];
  this.x = x;
  this.y = y;
  this.rInit = 15;
  this.r = this.rInit;
  this.i = i;

  this.xdir = 1;

  this.grow = function() {
    this.r = this.r + 2;
  }

  this.shiftDown = function() {
    this.xdir *= -1;
    this.y += this.rInit;
  }

  this.move = function() {
    this.x = this.x + this.xdir;
  }

  this.show = function() {
    if(sketch.frameCount % 60 < 30) {
      this.dot = this.dot1;
    }
    else {
      this.dot = this.dot2;
    }

    sketch.noStroke();
    sketch.fill(255);
    //sketch.ellipse(this.x, this.y, this.r*2, this.r*2);
    sketch.push();
    var d = 8;
    if(this.r > 20) {
      sketch.fill(255, 0, 0);
      sketch.translate(sketch.width / 2, sketch.height / 2);
      var x = sketch.cos(this.i * 0.2 + sketch.millis() * 0.002 * 1) * 200;
      var y = sketch.sin(this.i * 0.2 + sketch.millis() * 0.002 * 2) * 200;
      sketch.translate(sketch.floor(x / d) * d, sketch.floor(y / d) * d);
    }
    else {
      sketch.translate(sketch.floor(this.x / d) * d, sketch.floor(this.y / d) * d);
    }

    sketch.scale(this.r / 4);

    if(this.r > 20) {
      //sketch.rotate(sketch.millis() * 0.01);
    }

    sketch.translate(-this.dot[0].length * 0.5, -this.dot.length * 0.5);
    for(var i = 0; i < this.dot.length; i++) {
      for(var j = 0; j < this.dot[0].length; j++) {
        if(this.dot[i][j] == 1) {
          sketch.rect(j, i, 1, 1);
        }
      }
    }
    sketch.pop();
  }

}

function Ship(sketch) {
  this.x = sketch.width/2;
  this.xdir = 0;

  this.show = function() {
    sketch.fill(255);
    sketch.rectMode(sketch.CENTER);
    sketch.rect(this.x, sketch.height-20, 20, 60);
  }

  this.setDir = function(dir) {
    this.xdir = dir;
  }

  this.move = function(dir) {
    this.x += this.xdir*5;
  }

}


var s = function (sketch) {

  var ship;
  var flowers = [];
  var drops = [];

  sketch.setup = function () {
    sketch.createCanvas(600, 600);
    ship = new Ship(sketch);
    // drop = new Drop(width/2, height/2);
    for (var i = 0; i < 2; i++) {
      for (var j = 0; j < 6; j++) {
        var x = j * 80 + 80;
        var y = i * 60 + 60;
        if(i == 0) x += 40;
        flowers.push(new Flower(sketch, x, y, flowers.length));
      }
    }
  }

  sketch.draw = function () {
    sketch.background(0);

    for (var i = 0; i < drops.length; i++) {
      drops[i].show();
      drops[i].move();
      for (var j = 0; j < flowers.length; j++) {
        if (drops[i].hits(flowers[j])) {
          flowers[j].grow();
          drops[i].evaporate();
        }
      }
    }

    var edge = false;

    for (var i = 0; i < flowers.length; i++) {
      flowers[i].show();
      flowers[i].move();
      if (flowers[i].x > sketch.width - flowers[i].r || flowers[i].x < flowers[i].r) {
        edge = true;
      }
    }

    if (edge) {
      for (var i = 0; i < flowers.length; i++) {
        flowers[i].shiftDown();
      }
    }

    for (var i = drops.length - 1; i >= 0; i--) {
      if (drops[i].toDelete) {
        drops.splice(i, 1);
      }
    }

    ship.show();
    ship.move();

  }

  sketch.keyReleased = function () {
    if (sketch.key != ' ') {
      ship.setDir(0);
    }
  }


  sketch.keyPressed = function () {
    if (sketch.key == ' ') {
      var drop = new Drop(sketch, ship.x, sketch.height);
      drops.push(drop);
    }

    if (sketch.keyCode === sketch.RIGHT_ARROW) {
      ship.setDir(1);
    } else if (sketch.keyCode === sketch.LEFT_ARROW) {
      ship.setDir(-1);
    }
  }

};

var myp5 = new p5(s, document.getElementById('p5sketch'));
</script>