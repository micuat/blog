---
layout: post
title:  "Dead Reckoning"
author: naoto
categories: [ sketch ]
image: assets/images/2019-06-17-Dead-Reckoning.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

(Dumb) dead reckoning simulation for installation Open, Closed, Open at Jewish Museum Berlin.

<script>
//.parent("p5sketch")
function setup() {
  createCanvas(400, 400).parent("p5sketch");
}

let x = 100;
let y = 350;
let rot = 0;
let count = 1;
let stucking = 0;

function draw() {
  background(220);
  let cycle = 240;
  for (let i = 0; i < 100; i++) {
    let dx = 0;
    let dy = -1 + random(0.05);

    if (count % cycle > cycle-40) {
      dy = -dy;
    }

    x += Math.cos(rot) * dx - Math.sin(rot) * dy;
    y += Math.sin(rot) * dx + Math.cos(rot) * dy;

    if (count % cycle == 0) {
      rot -= Math.PI * randomGaussian(1,0.5) - random(-0, 0.1);
    }
    count++;
    
    let d = 5;
    let stuck = false;
    if(x < 0) {stuck = true; x=d;rot = lerp(rot,Math.PI / 2,0.99995);}
    if(x > width) {stuck = true; x=width-d;rot = lerp(rot,Math.PI * 0.75,0.99995);}
    if(y < 0) {stuck = true; y=d;rot = lerp(rot,0,0.99995);}
    if(y > height) {stuck = true; y=height-d;rot = lerp(rot,Math.PI,0.99995);}
    if(stuck) stucking++;
  }
  // ellipse(100, 200, 10, 10);
  if(frameCount % 30 == 0) {
    console.log(count, stucking, stucking / count);
  }
  translate(x, y);
  rotate(rot);
  let d = 10;
  beginShape();
  vertex(0, -d);
  vertex(-d, d);
  vertex(d, d);
  endShape(CLOSE);
}
</script>