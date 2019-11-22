---
layout: post
title:  "Unused Livecode Material"
author: naoto
categories: [ sketch ]
image: assets/images/2019-09-20-Unused-Livecode.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Created for code tutorial as a test during the residency at Hong Kong Academy for Performing Arts.

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

let slider;

function setup() {
  createCanvas(400, 400).parent("p5sketch");
  slider = createSlider(0, 8, 2).parent("p5sketch");
}

function draw() {
  let t = millis() * 0.001;
  let tt = t % 1;
  let tw = EasingFunctions.easeInOutCubic(tt);
  if(t % 2 > 1) tw = 1 - tw;
  background(0);
  rectMode(CENTER);
  let d = slider.value();
  for(let i = -d; i<= d; i++) {
    for(let j = -d; j<= d; j++) {
      push();
      fill(0, j * 10 + 150, i * 10 + 150);
      translate(width/2 + j * 25, height/2 + i * 25);
      rotate(tw * PI * 0.5 * j);
      rect(0, 0, 20, 10);
      pop();
    }
  }
}
</script>