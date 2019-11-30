---
layout: post
title:  "Motion Graphics Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2019-11-30-motion-graphics-study-1.png
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

function setup() {
  createCanvas(400, 400).parent("p5sketch");
  for(let i = 0; i < 32; i++) {
    positions[i] = {x: 0.5, y: 0.5, r: 0};
  }
}

let lastT = 0;
let colorIndex = 0;

function draw() {
  let t = millis() * 0.001;
  let w = width / 16;
  if (Math.floor(t) - Math.floor(lastT) > 0) {
    colorIndex++;
    if(colorIndex == 1) colorIndex++;
    if(colorIndex >= 5) colorIndex = 0;
    for(let i = 0; i < positions.length; i++) {
      let target = {ease: Power2.easeInOut, x: 0.5, y: 0.5, r: 0};
      switch(colorIndex) {
        case 0:
          break;
        case 3:
          target.r = Math.random() * Math.PI * 2;
          target.x = Math.random();
          target.y = Math.random();
          break;
        case 2:
        case 4:
          target.r = i / positions.length * 2 * Math.PI;
          target.x = Math.cos(target.r) * 0.4 + 0.5;
          target.y = Math.sin(target.r) * 0.4 + 0.5;
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
}
</script>