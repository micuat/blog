---
layout: post
title:  "Equation Study 10"
author: naoto
categories: [ sketch ]
tags: [ p5js, favoritenfestival ]
image: assets/images/2018-07-22-equation-study-10.png
description: "A sketch"
featured: true
comments: true
p5: true
---

Research for Favoriten Festival web residency with Janine Harrington.

[https://github.com/micuat/ashape/blob/master/064_equation/sketch.js](https://github.com/micuat/ashape/blob/master/064_equation/sketch.js)

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
var s = function (p) {
  let name;
  let startFrame;
  let cycle = 8.0;

  p.setup = function () {
    name = p.folderName;

    p.createCanvas(400, 400);
    p.frameRate(30);

    startFrame = p.frameCount;
  }

  function getCount() { return p.frameCount - startFrame };

  p.draw = function () {
    t = (getCount() / 30.0);
    if (getCount() % (30 * cycle) == 0) {

    }

    p.background(0);
    p.stroke(255);

    let n = 24;
    let l = p.width / n;
    for(let i = 0; i <= n; i++) {
      for(let j = 0; j <= n; j++) {
        p.push();
        p.translate(p.map(i, 0, n, 0, p.width), p.map(j, 0, n, 0, p.height))
        let dm = p.noise(i * 0.3, j * 0.2, t) - 0.5;
        dm = Math.pow(dm, 4.0);
        if(dm > 0)
        p.rotate(dm * 100.0);
        p.line(0, 0, l, 0);

        let dn = p.noise(i * 0.7, j * 0.6, t) - 0.5;
        dn = Math.pow(dn, 4.0);
        if(dn > 0)
        p.rotate(dn * 100.0);
        p.line(0, 0, 0, l);
        p.pop();
      }
    }
  }

};

var p063 = new p5(s, document.getElementById('p5sketch'));
</script>