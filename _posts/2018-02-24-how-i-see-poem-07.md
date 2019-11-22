---
layout: post
title:  "How I See Poem 07"
author: naoto
categories: [ sketch ]
tags: [ p5js, poem ]
image: assets/images/2018-02-24-How-I-See-Poem-07.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Text from The Sabbath by Abraham Joshua Heschel.

An outcome from Machine Learning Literacy workshop led by Hannah Davis at the School for Poetic Computation.

<script>
// The seventh day is a palace in time which we build. It is made of soul, of joy and reticence. In its atmosphere, a discipline is a reminder of adjacency to eternity. (from The Sabbath, Abraham Joshua Heschel)

var s = function (sketch) {
  var text = [];
  var index = 0;
  var lastT = 0;

  sketch.setup = function () {
    sketch.createCanvas(400, 400);

    text.push("The seventh day");
    text.push("is a palace");
    text.push("in time");
    text.push("which we build.");
    text.push("It is made of soul,");
    text.push("of joy and reticence.");
    text.push("In its atmosphere,");
    text.push("a discipline is");
    text.push("a reminder of adjacency");
    text.push("to eternity.");

    let myFont = sketch.loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');
    sketch.textFont(myFont);
    sketch.textSize(20);
  }
  sketch.draw = function () {
    sketch.background(0);
    let freq = 120;

    // sketch.translate(sketch.width/2, sketch.height/2);

    sketch.fill(255);

    sketch.translate(sketch.width / 2, sketch.height / 2);
    sketch.scale(0.5, 0.5);
    sketch.stroke(255);
    sketch.strokeWeight(2);
    let t = sketch.millis() * 0.001 % 10;
    let tt = t;
    let tmpIndex = index;
    if (t < 1) {
      t = 0.5;
      if (lastT > 1) index += 2;
      tmpIndex = index;
    }
    else if (t < 5) {
      t = sketch.map(t, 1, 5, 0.5, 4);
      tmpIndex = sketch.floor(sketch.millis() * 0.04);
    }
    else if (t < 6) {
      t = 4;
      if (lastT < 5) index += 2;
      tmpIndex = index;
    }
    else {
      t = sketch.map(t, 10, 6, 0.5, 4);
      tmpIndex = sketch.floor(sketch.millis() * 0.04);
    }
    lastT = tt;
    sketch.push();
    sketch.scale(1, 0.75);
    for (let j = -1; j < 1; j++) {
      for (let i = 0; i < 4; i++) {
        sketch.push();
        let s = (300 * t / 2) * j;
        sketch.translate(s, s);

        let l = 100;
        if (j == -1 && 300 * t / 2 > 250) l = 300 - t / 2 * 300;
        sketch.line(300, 300, 300, 300 - l);
        sketch.line(300, 300, 300 - l, 300);

        if (i % 2 == 0) {
        }
        else {
          if (j == 0) {
            let ti = (tmpIndex + (3 - i) / 2) % text.length;
            // if(i == 3)
            // {
            //   ti = (sketch.floor(sketch.frameCount / (freq)) + i + j * 4 + text.length) % text.length;
            // }
            sketch.scale(1 / 0.75, 1);
            sketch.noStroke();
            sketch.fill(255);
            sketch.text(text[ti], -150, 200);
            sketch.scale(0.75, 1)
          }
        }

        sketch.pop();
        sketch.rotate(-sketch.PI / 2);
      }
    }
    sketch.pop();
    sketch.blendMode(sketch.EXCLUSION);
    sketch.noStroke();
    if (tt < 1)
      sketch.rect(160, -160, 50, 320);
    else if (5 < tt && tt < 6)
      sketch.rect(-160, -160, -50, 320);

    sketch.blendMode(sketch.BLEND);
  }

};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>