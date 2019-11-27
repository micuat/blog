---
layout: post
title:  "Duchamp Study"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2018-10-16-duchamp-study.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
// https://gist.github.com/gre/1650294
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t * t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t * (2 - t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t * t * t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t) * t * t + 1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t * t * t * t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1 - (--t) * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t * t * t * t * t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1 + (--t) * t * t * t * t },
  // acceleration until halfway, then deceleration 
  easeInOutQuint: function (t) { return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t }
}

var n = 8;
var targets = [];
var coeffs = [];

for(let i = 0; i < n; i++) {
  targets[i] = 0;
  coeffs[i] = 0;
}

var S099 = function (p) {
  let startTime;
  let pg;
  let lastSeq;
  let cycle = 4.0;
  

  pg = p.createGraphics(400, 400, p.WEBGL);

  function drawPg(pg, t) {
    let seq = Math.floor(t / cycle);
    pg.push();
    pg.blendMode(p.BLEND);
    pg.background(0);

    // pg.translate(pg.width * 0.5, pg.height * 0.5);

    let phase = t / cycle % 1.0;

    let R = pg.width;

    pg.rotate(phase * 2 * Math.PI);

    pg.noFill();
    pg.strokeWeight(3);
    let r = R;

    pg.colorMode(p.HSB, 8, 8, 8);
    for(let i = 0; i < n; i++) {
      pg.push();
      let r0 = r - R / n / 2;
      let r1 = r0 - R / n / 2;

      pg.stroke(i, 3, 7);
      pg.ellipse(0, 0, r0, r0);
      coeffs[i] = p.lerp(coeffs[i], targets[i], 0.1);
      let x = (r0 - r1) / 2 * coeffs[i];
      pg.stroke(i, 7, 7);
      pg.rotate(i * Math.PI / n);
      pg.ellipse(x, 0, r1, r1);

      pg.push();
      let dist = Math.abs(x);
      if(dist > 1) {
        pg.fill(i, 7, 7);
        let rs = R / n / 4 * coeffs[i];
        pg.ellipse(x + r1 / 2, 0, rs, rs);
        pg.ellipse(x - r1 / 2, 0, rs, rs);
      }
      pg.pop();

      r = r1;
      pg.pop();
    }

    lastSeq = seq;
    pg.pop();
  }

  this.draw = function (t) {
    drawPg(pg, t);
    p.image(pg, 0, 0);
  }
};

var s = function (p) {
  let startTime;
  
  let s099 = new S099(p);

  p.setup = function () {
    name = p.folderName;

    p.createCanvas(400, 400);
    p.frameRate(30);

    startTime = p.millis();
  }

  function getTime() { return (p.millis() - startTime) * 0.001 };

  let lastT = 0;
  p.draw = function () {
    t = getTime();
    if(Math.floor(t) - Math.floor(lastT) > 0) {
      for(let i = 0; i < 8; i++) {
        targets[i] = Math.random() > 0.5 ? 1 : 0;
      }
    }
    lastT = t;
    s099.draw(t);
  }
};

var p = new p5(s, document.getElementById('p5sketch'));
</script>