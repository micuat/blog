---
layout: post
title:  "Equation Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, favoritenfestival ]
image: assets/images/2018-07-09-equation-study-1.png
description: "A sketch"
featured: true
comments: true
p5: true
---

Research for Favoriten Festival web residency with Janine Harrington.

[https://github.com/micuat/ashape/blob/master/052_equation/sketch.js](https://github.com/micuat/ashape/blob/master/052_equation/sketch.js)

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
var s = function (p) {
  let name;
  let currents = [];
  let n = 16 + 1;
  p.setup = function () {
    p.createCanvas(400, 400);
    p.frameRate(30);
    for(let k = 0; k < 20; k++) {
      currents.push(p.floor(p.random(n * n)));
    }
  }

  p.draw = function () {
    p.background(0);
    p.stroke(255);

    if(p.frameCount % 60 == 0) {
      currents = [];
      for(let k = 0; k < 20; k++) {
        currents.push(p.floor(p.random(n * n)));
      }
    }

    let theta = p.frameCount / 30.0 * 0.5 * 2 * Math.PI;
    for(let i = 0; i < n; i++) {
      for(let j = 0; j < n; j++) {
        let l = p.width / (n - 1);
        let hl = l * 0.5;
        p.push();
        p.translate((j + 0) * l, (i + 0) * l);

        for(let k in currents) {
          if(i * n + j == currents[k])
            hl *= 1.0 - p.pow(1.0 - p.map(Math.cos(theta), -1, 1, 0, 1), 4.0);
        }
        p.line(-hl, 0, hl, 0);
        p.line(0, -hl, 0, hl);
        p.pop();
      }
    }
  }
};

var p052 = new p5(s, document.getElementById('p5sketch'));
</script>