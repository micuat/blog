---
layout: post
title:  "Line Effect Study"
author: naoto
categories: [ sketch ]
image: assets/images/2019-09-08-Line-Effect-Study.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
//.parent("p5sketch");
/*
 * Easing Functions - inspired from http://gizma.com/easing/
 * only considering the t value for the range [0, 1] => [0, 1]
 */
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t*t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t*(2-t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t<.5 ? 2*t*t : -1+(4-2*t)*t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t*t*t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t)*t*t+1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t<.5 ? 4*t*t*t : (t-1)*(2*t-2)*(2*t-2)+1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t*t*t*t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1-(--t)*t*t*t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t<.5 ? 8*t*t*t*t : 1-8*(--t)*t*t*t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t*t*t*t*t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1+(--t)*t*t*t*t },
  // acceleration until halfway, then deceleration 
  easeInOutQuint: function (t) { return t<.5 ? 16*t*t*t*t*t : 1+16*(--t)*t*t*t*t }
}

const ef = EasingFunctions;

function createPalette(_url) {
  let slash_index = _url.lastIndexOf('/');
  let palatte_str = _url.slice(slash_index + 1);
  let arr = palatte_str.split('-');
  return arr.map(a => color('#' + a));
}

class Particle {
  Particle({x, y, vx, vy, life}) {
    this.x = x
    this.y = y
    this.vx = vx
    this.vy = vy
    this.life = life
  }
  draw() {
    let x = this.x
    let y = this.y
    this.x += this.vx
    this.y += this.vy
    line(x, y, this.x, this.y)
    this.life--
  }
  isAlive() {
    return this.life > 0
  }
}

const particles = []

class A {
  time() {
    return millis() * 0.001 * 0.5;
  }
  line(x0, y0, x1, y1) {
    let t = this.time();
    if (t % 2 < 1) {
      t = t % 1;
      t = ef.easeInOutCubic(t);
      x1 = lerp(x0, x1, t)
      y1 = lerp(y0, y1, t)
    } else {
      t = (t % 1);
      t = ef.easeInOutCubic(t);
      x0 = lerp(x0, x1, t)
      y0 = lerp(y0, y1, t)
      x0 = [x1, x1 = x0][0];
      y0 = [y1, y1 = y0][0];
    }
    line(x0, y0, x1, y1)
    let th = PI+atan2(y1-y0, x1-x0)
    // if(frameCount%2==0)
    for (let i = 0; i < 1; i++) {
      let theta = (random(1)>0.5?-0.25:0.25) * PI + random(-0.25,0.25)*0.125*PI + th;
      let r = random(2) * (t > 0.5 ? 2 - t * 2 : t * 2);
      let vx = r * cos(theta);
      let vy = r * sin(theta);
      let x = x1
      let y = y1
      // point(x + vx, y + vy);
      let p = new Particle({x, y, vx, vy, life: 60})
      p.x = x
      p.y = y
      p.vx = vx
      p.vy = vy
      p.life = 10
      particles.push(p)
    }
  }
  rect(x, y, w, h) {
    this.line(x, y, x + w, y);
    this.line(x + w, y, x + w, y + h);
    this.line(x + w, y + h, x, y + h);
    this.line(x, y + h, x, y);
  }
}

let a;
let pal;

function setup() {
  createCanvas(400, 400).parent("p5sketch");
  a = new A();
  pal = createPalette('https://coolors.co/0d3b66-faf0ca-f4d35e-ee964b-f95738');
}

function draw() {
  background(pal[2]);

  if(frameCount % 30 == 0) {
    //console.log(frameRate())
  }
  
  // a.line(0, 0, 100, 100);

  stroke(pal[4]);
  let W = width / 4;
  let w = W * 0.9;
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      a.rect(W * j, W * i, w, w);
    }
  }

  // translate(width/2, height/2);
  // let n = 16
  // for (let i = 0; i < n; i++) {
  //   let r = width/3
  //   let t0 = i / n * 2 * PI
  //   let x0 = r * cos(t0)
  //   let y0 = r * sin(t0)
  //   let t1 = (i+1) / n * 2 * PI
  //   let x1 = r * cos(t1)
  //   let y1 = r * sin(t1)
  //   a.line(x0, y0, x1, y1)
  // }
  
  for (let i = particles.length - 1; i >= 0; i--) {
    particles[i].draw()
    // if(particles[i].isAlive() == false) {
    //   particles.splice(i, 1)
    // }
  }
  while(particles[0] != undefined && particles[0].isAlive() == false) {
    particles.pop()
  }
}
</script>