---
layout: post
title:  "Penguin Eyes"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2017-11-11-penguin-eyes.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<script>
const s = ( p ) => {
  p.setup = () => {
    p.createCanvas(400, 400);
    p.noCursor();
  }
  p.draw = () => {
    p.background(240);
    p.push();
    p.fill(20);
    p.noStroke();
    p.translate(p.width/2, p.height/2);
    p.ellipse(0, 0, 300, 300);
    p.ellipse(0, 270, 400, 300);
    p.fill(240);
    p.ellipse(0, 35, 230, 230);
    p.ellipse(0, 290, 370, 300);
    p.fill(20);
    p.ellipse(-50, 30, 30, 30);
    p.ellipse( 50, 30, 30, 30);
    p.fill(240);
    let dx = p.map(p.mouseX + 50, 0, p.width, -5, 5);
    let dy = p.map(p.mouseY - 30, 0, p.height, -5, 5);
    p.ellipse(-50 + dx, 30 + dy, 10, 10);
    dx = p.map(p.mouseX - 50, 0, p.width, -5, 5);
    dy = p.map(p.mouseY - 30, 0, p.height, -5, 5);
    p.ellipse( 50 + dx, 30 + dy, 10, 10);
    p.stroke(20);
    p.strokeWeight(2);
    p.line(-80, 10, -30, -20);
    p.line(80, 10, 30, -20);
    p.fill(200, 200, 30);
    p.ellipse(0, 90, 100, 50);
    p.pop();
    p.noStroke();
    p.fill(150, 150, 250);
    p.translate(p.mouseX, p.mouseY);
    p.rotate(Math.atan2(p.mouseY-p.height/2-30, p.mouseX-p.width/2));
    p.ellipse(0, 0, 70, 20);
    p.push();
    p.translate(0 + 35, 0);
    p.rotate(p.map(Math.sin(p.frameCount*0.2), -1, 1, -0.2, 0.2));
    p.ellipse(10, 0, 20, 10);
    p.pop();
    p.fill(20);
    p.ellipse(0-20, 0, 5, 5);
    p.fill(240);
    p.ellipse(0-20, 0, 2, 2);
  }
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>