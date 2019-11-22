---
layout: post
title:  "How I See Poem 01"
author: naoto
categories: [ sketch ]
tags: [ p5js, poem ]
image: assets/images/2018-02-16-How-I-See-Poem-01.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Text from Between the World and Me by Ta-Nehisi Coates.

An outcome from Machine Learning Literacy workshop led by Hannah Davis at the School for Poetic Computation.

<script>
// Mistakes were made. Bodies were broken. People were enslaved. We meant well. We tried our best. "Good intention" is a hall pass through history, a sleeping pill that ensures the Dream.
var s = function(sketch) {
  var pts = [];
  var p0s = [];
  var p1s = [];

  var text = [];

  sketch.setup = function() {
    sketch.createCanvas(400, 400);

    text.push("Mistakes were made.");
    text.push("Bodies were broken.");
    text.push("People were enslaved. ");
    text.push("We meant well.");
    text.push("We tried our best.");
    text.push("\"Good intention\" is");
    text.push("a hall pass");
    text.push("through history,");
    text.push("a sleeping pill");
    text.push("that ensures the Dream.");

    let myFont = sketch.loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');

    sketch.textFont(myFont);
    sketch.textSize(16);

    pts.push(sketch.createVector(0, 0));

    for (let i = 0; i < 16; i++) {
      pts.push(sketch.createVector(sketch.random(-200, 200), sketch.random(-200, 200)));
    }

    for (let i = 0; i < 5; i++) {
      p0s.push(pts[0]);
      p1s.push(pts[1]);
    }
  }
  sketch.draw = function() {
    sketch.background(0);
    let freq = 60;

    sketch.translate(sketch.width / 2, sketch.height / 2);

    let ti = sketch.floor(sketch.frameCount / (freq)) % text.length;
    // sketch.text(text[ti], 0, 700);
    // let p0 = sketch.random(pts);
    // p0 = pts[0];

    for (let i = 0; i < p0s.length; i++) {
      if ((sketch.frameCount + i * 6 * 2) % freq == 0) {
        p0s[i] = p1s[i];
        if ((sketch.frameCount + i * 6 * 2) % (freq * 4) == 0) {
          p1s[i] = pts[0];
        } else {
          do {
            p1s[i] = sketch.random(pts);
          } while (p0s[i] == p1s[i] || p1s[i] == pts[0])
        }
      }
      let p0 = p0s[i];
      let p1 = p1s[i];
      sketch.stroke(255);
      if (i != 0) {
        sketch.stroke(255, 128);
      }
      // sketch.point(pts[0].x, pts[0].y);
      let t = ((sketch.frameCount + i * 6 * 2) % freq) / freq;
      let x0 = p0.x;
      let y0 = p0.y;
      let dx = p1.x - x0;
      let dy = p1.y - y0;
      let r = sketch.sqrt(dx * dx + dy * dy);
      let theta = sketch.atan2(dy, dx);
      sketch.push();
      sketch.translate(x0, y0);
      sketch.rotate(theta);

      sketch.push();
      sketch.fill(255);
      sketch.noStroke();
      if (i == 0)
        sketch.text(text[ti], 0, 20);
      sketch.pop();

      let d = 7;
      if (t < 0.25) {
        let x = sketch.lerp(0, r, t * 2);
        sketch.line(0, 0, x, d);
        sketch.line(0, 0, x, -d);

        // sketch.fill(255);
        // sketch.beginShape();
        // sketch.vertex(0, 0);
        // sketch.vertex(x, d);
        // sketch.vertex(x, -d);
        // sketch.endShape();
      } else if (t < 0.5) {
        sketch.line(0, 0, r / 2, d);
        sketch.line(0, 0, r / 2, -d);
        let x = sketch.lerp(0, r, t * 2);
        sketch.line(r / 2, d, x, 0);
        sketch.line(r / 2, -d, x, 0);

        // sketch.fill(255);
        // sketch.beginShape();
        // sketch.vertex(r/2, d);
        // sketch.vertex(0, 0);
        // sketch.vertex(r/2, -d);
        // sketch.vertex(x, 0);
        // sketch.vertex(r/2, d);
        // sketch.endShape();
      } else if (t < 0.75) {
        sketch.line(r / 2, d, r, 0);
        sketch.line(r / 2, -d, r, 0);
        let x = sketch.lerp(0, r, (t - 0.5) * 2);
        sketch.line(x, 0, r / 2, d);
        sketch.line(x, 0, r / 2, -d);

        // sketch.fill(255);
        // sketch.beginShape();
        // sketch.vertex(r/2, d);
        // sketch.vertex(r, 0);
        // sketch.vertex(r/2, -d);
        // sketch.vertex(x, 0);
        // sketch.endShape();
      } else {
        let x = sketch.lerp(0, r, (t - 0.5) * 2);
        sketch.line(x, d, r, 0);
        sketch.line(x, -d, r, 0);

        // sketch.fill(255);
        // sketch.beginShape();
        // sketch.vertex(r, 0);
        // sketch.vertex(x, d);
        // sketch.vertex(x, -d);
        // sketch.endShape();
      }
      sketch.pop();
    }
  }

};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>