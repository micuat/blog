---
layout: post
title:  "Motion Graphics Study 2"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2019-12-05-motion-graphics-study-2.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
var colorSchemes = [
  new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f"),
];

function ColorScheme(colorString) {
  this.colors = [];
  {
    let cc = colorString.split("/");
    let cs = cc[cc.length - 1].split("-");
    for (let i in cs) {
      let r = parseInt("0x" + cs[i].substring(0, 2));
      let g = parseInt("0x" + cs[i].substring(2, 4));
      let b = parseInt("0x" + cs[i].substring(4, 6));
      this.colors.push({ r: r, g: g, b: b });
    }
  }
  this.offset = 0;
}

ColorScheme.prototype.get = function (i) {
  i = Math.min(this.colors.length - 1, Math.max(0, i));
  return this.colors[(i + this.offset) % this.colors.length];
}

function setColor(parent, func, index, alpha) {
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[0].get(index).r, colorSchemes[0].get(index).g, colorSchemes[0].get(index).b, alpha);
}

const positions = [];
let font;
let points;
let bounds;

function preload() {
  font = loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');
}

function setup() {
  createCanvas(400, 400).parent("p5sketch");;
  const text = '@'
  points = font.textToPoints(text, 0, 0, 10, {
    sampleFactor: 3,
    simplifyThreshold: 0
  });
  for(let i = 0; i < points.length; i++) {
    positions[i] = {x: 0.5, y: 0.5, r: 0};
  }
  bounds = font.textBounds(text, 0, 0, 10);
}

let lastT = 0;
let colorIndex = 0;

function draw() {
  let t = millis() * 0.001;
  let w = width / 32;
  if (Math.floor(t) - Math.floor(lastT) > 0) {
    colorIndex++;
    if(colorIndex == 1) colorIndex++;
    if(colorIndex >= 5) colorIndex = 0;
    for(let i = 0; i < positions.length; i++) {
      let target = {ease: Power2.easeInOut, x: 0.5, y: 0.5, r: 0};
      let theta, R;
      let x = map(points[i%points.length].x, bounds.x, bounds.x+bounds.w, 0, 1);
      let y = map(points[i%points.length].y, bounds.y, bounds.y+bounds.h, 0, 1) / bounds.w * bounds.h;
      target.r = atan2(y-0.5, x-0.5);
      switch(colorIndex) {
        case 0:
          theta = target.r;
          R = randomGaussian() * 0.1 + 0.4;
          target.r = theta;
          target.x = Math.cos(theta) * R + 0.5;
          target.y = Math.sin(theta) * R + 0.5;
          break;
        case 3:
          theta = target.r;
          R = abs(randomGaussian() * 0.2);
          target.x = Math.cos(theta) * R + 0.5;
          target.y = Math.sin(theta) * R + 0.5;
          break;
        case 2:
          target.x = x;
          target.y = y;
          break;
        case 4:
          target.x = Math.cos(target.r) * 0.4 + 0.5;
          target.y = Math.sin(target.r) * 0.4 + 0.5;
          target.r -= 2*Math.PI;
          break;
      }
      TweenLite.to(positions[i], 1, target);
    }
  }
  lastT = t;
  setColor(this, 'background', colorIndex);
  setColor(this, 'fill', 1);
  noStroke();
  for(let i = 0; i < positions.length; i++) {
    const pos = positions[i];
    push();
    translate(pos.x * width, pos.y * height);
    rectMode(CENTER);
    rotate(pos.r);
    rect(0, 0, w, w);
    pop();
  }
  // push();
  // noFill();
  // setColor(this, 'stroke', 1);
  // beginShape();
  // for(let i = 0; i < positions.length; i++) {
  //   const pos = positions[i];
  //   vertex(pos.x * width, pos.y * height);
  // }
  // endShape();
  // pop();
}
</script>