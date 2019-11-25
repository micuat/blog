---
layout: post
title:  "RIDM Festival Performance"
author: naoto
categories: [ sketch ]
tags: [ p5js, josefalbers ]
image: assets/images/2017-11-14-RIDM-Festival-Performance.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

A sketch for a performance at RIDM Festival with Olivier Lalonde.

<script>
class Particle {
  constructor(_initT) {
    this.t = 0;
    this.initT = 0;
    this.r = 100;
    this.rInit = 100;
    this.z = 0;
    this.R = 5;
    this.x = 0;
    this.y = 0;

    this.doNormal = true;
    this.doRandom = false;

    this.pNext;
    this.pPrev;

    this.initT = _initT;
    this.t = _initT;

    this.x = this.r * Math.cos(this.t);
    this.y = this.r * Math.sin(this.t);
  }

  sine() {
    this.doNormal = false;
    this.doRandom = false;
  }

  normal() {
    this.doNormal = true;
    this.doRandom = false;
  }

  rand() {
    this.doNormal = false;
    this.doRandom = true;
  }

  update(millis) {
    const p = myp5;
    if (this.doNormal) {
      this.r = p.lerp(this.r, this.pNext.r, 0.95);
      this.z = p.lerp(this.z, this.pNext.z, 0.95);

      this.r = p.lerp(this.r, this.rInit, 0.01);
      this.z = p.lerp(this.z, 0, 0.002);
    } else if (this.doRandom) {
      this.r = p.lerp(this.r, p.map(Math.random(), 0, 1, 50, 150), 0.2);
      this.z = p.lerp(this.z, p.map(Math.random(), 0, 1, -40, 40), 0.2);
    } else {
      let a = Math.sin(p.map(millis, 0, 200, 0, 2 * Math.PI) + this.initT * 4);
      this.r = 100 + a * 30;
      this.z = Math.cos(p.map(millis, 0, 200, 0, 2 * Math.PI) + this.initT * 4) * 100;
    }

    this.t = p.map(millis, 0, 5000, 0, 2 * Math.PI) + this.initT;

    this.x = this.r * -Math.sin(this.t);
    this.y = this.r * Math.cos(this.t);
  }

  draw() {
    const p = myp5;
    p.push();

    //p.translate(0, 0, this.z);

    // p.rotate(this.t);

    // p.translate(0, this.r);

    p.fill(255, params.fillColor * p.map(fader, 0, 128, 0, 1));
    p.noStroke();
    // p.rotate(-this.t);
    // p.rotateX(-params.rx);
    p.ellipse(this.x, this.y * Math.cos(params.rx) - this.z * Math.sin(params.rx), this.R, this.R);

    p.pop();

    //stroke(255, lineColor);
    //line(x * 0.95, y * 0.95, z, x, y, z);
  }
}

var fader = 128;
var d0 = 10,
  d1 = 0,
  d2 = 0;

var params = {
  rx: 0,
  lineColor: 0,
  fillColor: 255
};

const particles = [];
const n = 128;

let lastState = -1;


const s = (p) => {
  p.setup = () => {
    p.createCanvas(400, 400);
    //size(1920, 1080, P3D);
    //fullScreen(P3D, 2);

    // p.ortho();
    p.smooth();

    for (let i = 0; i < n; i++) {
      particles.push(new Particle(i / n * 2 * Math.PI));
    }

    for (let i = 0; i < n; i++) {
      particles[i].pPrev = particles[(i - 1 + n) % n];
      particles[i].pNext = particles[(i + 1) % n];
    }

  }

  let lastT = 0;
  p.draw = () => {
    p.background(0);

    const t = p.millis() * 0.001;
    if (Math.floor(t / 2) - Math.floor(lastT / 2) > 0) {
      const a = Math.random();
      if (a < 0.33)
        particles[64].sine();
      else if(a < 0.67)
        for (const pt of particles)
          pt.normal();
      else
        for (const pt of particles)
          pt.rand();

      if (Math.random() > 0.5)
        TweenLite.to(params, 1.5, {
          rx: -Math.PI / 2
        });
      else
        TweenLite.to(params, 1.5, {
          rx: 0
        });

      if (Math.random() > 0.5)
        TweenLite.to(params, 1.5, {
          lineColor: 255,
          fillColor: 0
        });
      else
        TweenLite.to(params, 1.5, {
          lineColor: 0,
          fillColor: 255
        });

      // let state = Math.floor(p.random(2));
      // if (state == 0 && lastState != 0) {
      //   TweenLite.to(params, 1.5, {
      //     rx: -Math.PI / 2,
      //     lineColor: 0,
      //     fillColor: 255
      //   });
      //   for (const pt of particles)
      //     pt.normal();
      //   lastState = 0;
      // }
      // if (state == 1 && lastState != 1) {
      //   TweenLite.to(params, 1.5, {
      //     rx: 0,
      //     lineColor: 0,
      //     fillColor: 255
      //   });
      //   for (const pt of particles)
      //     pt.normal();
      //   particles[Math.floor(n / 2)].sine();
      //   lastState = 1;
      // }
      // if (state == 2 && lastState != 2) {
      //   TweenLite.to(params, 1.5, {
      //     rx: 0,
      //     lineColor: 255,
      //     fillColor: 0
      //   });
      //   for (const pt of particles)
      //     pt.rand();
      //   lastState = 2;
      // }
    }    

    p.translate(p.width / 2, p.height / 2);
    p.strokeWeight(2);

    const sc = p.map(params.rx, 0, -Math.PI / 2, 1, 0);

    let m = p.millis();
    for (const pt of particles)
      pt.update(m);

    for (const pt of particles)
      pt.draw();

    p.stroke(255, params.lineColor * p.map(fader, 0, 128, 0, 1));
    p.noFill();
    p.beginShape();
    p.curveVertex(particles[0].x, particles[0].y * Math.cos(params.rx) - particles[0].z * Math.sin(params.rx), particles[0].y * Math.sin(params.rx) + particles[0].z * Math.cos(params.rx));
    for (const pt of particles) {
      p.curveVertex(pt.x, pt.y * Math.cos(params.rx) - pt.z * Math.sin(params.rx));
    }
    p.curveVertex(particles[0].x, particles[0].y * Math.cos(params.rx) - particles[0].z * Math.sin(params.rx));
    p.curveVertex(particles[0].x, particles[0].y * Math.cos(params.rx) - particles[0].z * Math.sin(params.rx));
    p.endShape();

    lastT = t;
  }

  p.keyPressed = () => {
    switch (p.key) {
      case '1':
        particles[64].sine();
        break;
      case '2':
        for (const pt of particles)
          pt.normal();
        break;
      case '3':
        for (let i = 0; i < particles.length; i++) {
          const pt = particles[i];
          pt.r += 200 * -Math.exp(-(i - 64.0) * (i - 64.0) / 10.0);
          pt.z += 800 * Math.sin(i / n * 8 * Math.PI) * -Math.exp(-(i - 64.0) * (i - 64.0) / 10.0);
        }
        break;
      case '4':
        for (const pt of particles)
          pt.rand();
        break;
      case '5':
        TweenLite.to(params, 1.5, {
          rx: -Math.PI / 2
        });
        break;
      case '6':
        TweenLite.to(params, 1.5, {
          rx: 0
        });
        break;
      case '7':
        TweenLite.to(params, 1.5, {
          lineColor: 255,
          fillColor: 0
        });
        break;
      case '8':
        TweenLite.to(params, 1.5, {
          lineColor: 0,
          fillColor: 255
        });
        break;
    }
  }
}
let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>