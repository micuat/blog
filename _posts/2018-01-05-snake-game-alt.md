---
layout: post
title:  "Snake Game Alt"
author: naoto
categories: [ sketch ]
tags: [ p5js, danshiffman, remix ]
image: assets/images/2018-01-05-snake-game-alt.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Use arrow keys to control.

<script>
// Daniel Shiffman
// http://codingtra.in
// http://patreon.com/codingtrain
// Code for: https://youtu.be/AaGK-fj-BAM

// instance mode by Naoto Hieda

function Snake(sketch, scl) {
  this.x = 0;
  this.y = 0;
  this.xspeed = 1;
  this.yspeed = 0;
  this.total = 0;
  this.toAdd = 0;
  this.tail = [];

  this.eat = function(pos) {
    var d = sketch.dist(this.x, this.y, pos.x, pos.y);
    if (d < 1) {
      this.toAdd = 5;
      return true;
    } else {
      return false;
    }
  }

  this.dir = function(x, y) {
    this.xspeed = x;
    this.yspeed = y;
  }

  this.death = function() {
    for (var i = 0; i < this.tail.length; i++) {
      var pos = this.tail[i];
      var d = sketch.dist(this.x, this.y, pos.x, pos.y);
      if (d < 1) {
        console.log('starting over');
        this.total = 0;
        this.tail = [];
      }
    }
  }

  this.update = function(tiles) {
    if(sketch.frameCount % 10 != 0) return;

    for (var i = 0; i < this.tail.length - 1; i++) {
      this.tail[i] = this.tail[i + 1];
      var x = this.tail[i].x / scl;
      var y = this.tail[i].y / scl;
      tiles[y][x].pColor = 0;
    }
    if(this.toAdd > 0) {
      this.total++;
      this.toAdd--;
    }
    if (this.total >= 1) {
      this.tail[this.total - 1] = sketch.createVector(this.x, this.y);

      var x = this.tail[this.total - 1].x / scl;
      var y = this.tail[this.total - 1].y / scl;
      tiles[y][x].pColor = 0;
    }

    this.x = this.x + this.xspeed * scl;
    this.y = this.y + this.yspeed * scl;

    this.x = sketch.constrain(this.x, 0, sketch.width - scl);
    this.y = sketch.constrain(this.y, 0, sketch.height - scl);

    var j = this.x / scl;
    var i = this.y / scl;
    tiles[i][j].pColor = 0;
  }

  this.show = function() {
    // sketch.fill(255);
    // for (var i = 0; i < this.tail.length; i++) {
    //   sketch.rect(this.tail[i].x, this.tail[i].y, scl, scl);
    // }
    // sketch.rect(this.x, this.y, scl, scl);
  }
}


var sk = function (sketch) {

  var s;
  var scl = 20;

  var food;

  function Tile (sketch, j, i) {
    this.j = j;
    this.i = i;
    this.x = j * scl;
    this.y = i * scl;
    this.color = 255;
    this.pColor = 255;
    this.angle = 0;

    this.update = function () {
    }

    this.show = function () {
      var c = this.pColor / 8 * 7;
      c += tiles[(this.i+1)%tiles.length][this.j].pColor / 8 / 4;
      c += tiles[(this.i-1+tiles.length)%tiles.length][this.j].pColor / 8 / 4;
      c += tiles[this.i][(this.j+1)%tiles.length].pColor / 8 / 4;
      c += tiles[this.i][(this.j-1+tiles[i].length)%tiles.length].pColor / 8 / 4;
      this.color = c;
      this.color += 1;
      if(this.color > 255) this.color = 255;
      if(this.color < 0) this.color = 0;

      sketch.push();
      sketch.fill(255-this.color);
      sketch.translate(this.x + scl / 2, this.y + scl / 2);
      this.angle += (255-this.color) * 0.001;
      sketch.rotate(this.angle);
      sketch.rect(2 - scl/2, 2 - scl/2, scl - 4, scl - 4);
      sketch.pop();
      this.pColor = this.color;
    }
  }
  var tiles;

  sketch.setup = function () {
    sketch.createCanvas(400, 400);
    s = new Snake(sketch, scl);
    sketch.frameRate(60);
    sketch.pickLocation();

    tiles = [];

    for(var y = 0; y < sketch.height / scl; y++) {
      tiles[y] = [];
      for(var x = 0; x < sketch.width / scl; x ++) {
        tiles[y][x] = new Tile(sketch, x, y);
      }
    }
  }

  sketch.pickLocation = function () {
    var cols = sketch.floor(sketch.width / scl);
    var rows = sketch.floor(sketch.height / scl);
    food = sketch.createVector(sketch.floor(sketch.random(cols)), sketch.floor(sketch.random(rows)));
    food.mult(scl);
  }

  sketch.mousePressed = function () {
    s.toAdd = 5;
  }

  sketch.draw = function () {
    sketch.background(0);
    // sketch.stroke(0);
    sketch.noStroke();

    var j = food.x / scl;
    var i = food.y / scl;
    tiles[i][j].pColor = 0;
    tiles[i][j].color = 0;

    // for(var i = 0; i < tiles.length; i++) {
    //   for(var j = 0; j < tiles[i].length; j++) {
    //     tiles[i][j].update();
    //   }
    // }
    for(var i = 0; i < tiles.length; i++) {
      for(var j = 0; j < tiles[i].length; j++) {
        tiles[i][j].show();
      }
    }

    if (s.eat(food)) {
      sketch.pickLocation();
    }
    s.death();
    s.update(tiles);
    s.show();


    // sketch.fill(55, 0, 100);
    // sketch.rect(food.x, food.y, scl, scl);

  }

  sketch.keyPressed = function () {
    if (sketch.keyCode === sketch.UP_ARROW) {
      s.dir(0, -1);
    } else if (sketch.keyCode === sketch.DOWN_ARROW) {
      s.dir(0, 1);
    } else if (sketch.keyCode === sketch.RIGHT_ARROW) {
      s.dir(1, 0);
    } else if (sketch.keyCode === sketch.LEFT_ARROW) {
      s.dir(-1, 0);
    }
  }

};

var myp5 = new p5(sk, document.getElementById('p5sketch'));
</script>