---
layout: post
title:  "Minilab Livecode"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2017-02-17-minilab-livecode.png
description: "A sketch"
featured: true
comments: true
p5: true
---

A sketch for a display at the Society for Art and Technology.

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>

function Agent() {
  this.size = 10;
  this.x = 0;
  this.y = 0;
  this.z = 0;
  this.j = 0;
  this.i = 0;
  this.left = null;
  this.right = null;
  this.up = null;
  this.down = null;

  this.draw = function() {}
}
const agents = {};

const s = (p) => {
  p.setup = () => {
    p.createCanvas(400, 400, p.WEBGL);
    var i, j;
    for (i = -16; i < 16; i++) {
      for (j = -16; j < 16; j++) {
        agents[i + "," + j] = new Agent();
        agents[i + "," + j].size = 17 + i;
        agents[i + "," + j].j = j;
        agents[i + "," + j].i = i;
        agents[i + "," + j].x = p.width * Math.cos(j / 16 * Math.PI);
        agents[i + "," + j].y = i * 50;
        agents[i + "," + j].z = p.width * Math.sin(j / 16 * Math.PI);

        if (i > -16) {
          agents[i + "," + j].up = agents[(i - 1) + "," + j];
          agents[(i - 1) + "," + j].down = agents[i + "," + j];
        }
        if (j > -16) {
          agents[i + "," + j].left = agents[i + "," + (j - 1)];
          agents[i + "," + (j - 1)].right = agents[i + "," + j];
        }
        if (j == 15) {
          agents[i + "," + 15].right = agents[i + "," + (-16)];
          agents[i + "," + (-16)].left = agents[i + "," + 15];
        }
      }
    }
  }

  p.draw = () => {
    p.background(0);

    p.translate(p.width / 2, p.height / 2);
    p.rotateY(p.frameCount / 200)
    p.pointLight(255, 255, 255, 0, 0, 0);

    for (var key in agents) {
      const agent = agents[key];
      agent.draw = function() {
        p.push();
        p.translate(this.x, this.y, this.z);
        switch ((this.i + 16) % 3) {
          case 0:
            p.rotateZ(p.frameCount / 200);
          case 1:
            p.rotateY(-p.frameCount / 100);
          case 2:
            p.rotateX(p.frameCount / 300);
        }
        p.stroke(255);
        p.fill(255);
        //this.size = 17 + this.i;
        this.size += Math.sin(p.frameCount / (45 + this.j)) * 1;
        p.box(this.size);
        p.pop();
        // if(this.right != null)
        //   line(this.x, this.y, this.right.x, this.right.y);
        // if(this.down != null)
        //   line(this.x, this.y, this.down.x, this.down.y);
      }

      agent.draw();
    }
  }

}
let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>