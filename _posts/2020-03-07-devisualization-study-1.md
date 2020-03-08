---
layout: post
title:  "Devisualization Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2020-03-07-devisualization-study-1.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

A conceptual experiment to decompose data visualization.

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/
class ColorScheme {
  constructor(colorString) {
    this.colors = []; {
      let cc = colorString.split("/");
      let cs = cc[cc.length - 1].split("-");
      for (let i in cs) {
        this.colors.push("#" + cs[i]);
      }
    }
  }
  get(i) {
    return this.colors[i % this.colors.length];
  }
}

// https://gist.github.com/gre/1650294
const EasingFunctions = {
  // no easing, no acceleration
  linear: function(t) {
    return t
  },
  // accelerating from zero velocity
  easeInQuad: function(t) {
    return t * t
  },
  // decelerating to zero velocity
  easeOutQuad: function(t) {
    return t * (2 - t)
  },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function(t) {
    return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t
  },
  // accelerating from zero velocity 
  easeInCubic: function(t) {
    return t * t * t
  },
  // decelerating to zero velocity 
  easeOutCubic: function(t) {
    return (--t) * t * t + 1
  },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function(t) {
    return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1
  },
  // accelerating from zero velocity 
  easeInQuart: function(t) {
    return t * t * t * t
  },
  // decelerating to zero velocity 
  easeOutQuart: function(t) {
    return 1 - (--t) * t * t * t
  },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function(t) {
    return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t
  },
  // accelerating from zero velocity
  easeInQuint: function(t) {
    return t * t * t * t * t
  },
  // decelerating to zero velocity
  easeOutQuint: function(t) {
    return 1 + (--t) * t * t * t * t
  },
  // acceleration until halfway, then deceleration 
  easeInOutQuint: function(t) {
    return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t
  }
}

const colorScheme = new ColorScheme('https://coolors.co/ebebd3-083d77-da4167-f4d35e-f78764');

// https://jsfiddle.net/gh/get/library/pure/highcharts/highcharts/tree/master/samples/highcharts/demo/line-labels/
// WorldClimate.com
const series = [{
  name: 'Tokyo',
  data: [7.0, 6.9, 9.5, 14.5, 18.4, 21.5, 25.2, 26.5, 23.3, 18.3, 13.9, 9.6]
}, {
  name: 'London',
  data: [3.9, 4.2, 5.7, 8.5, 11.9, 15.2, 17.0, 16.6, 14.2, 10.3, 6.6, 4.8]
}]

function setup() {
  createCanvas(400, 400).parent('p5sketch');
  pixelDensity(1);
  frameRate(15);
}

function draw() {
  const t = millis() * 0.001;
  background(colorScheme.get(0));
  noFill();
  strokeWeight(3);

  const twr = EasingFunctions.easeInOutCubic(frameCount % 90 >= 45 ? 1 - (frameCount % 45) / 45 : (frameCount % 45) / 45);
  const tw = frameCount % 90 >= 45 ? 1 - (frameCount % 45) / 45 : (frameCount % 45) / 45;
  const N = 12 * map(EasingFunctions.easeInOutCubic(tw), 0, 1, 1, 12);

  translate(width / 2, height / 2);
  const sx = lerp(8, 1, 1 - pow(1 - twr, 16));
  
  for (let j = 0; j < 2; j++) {
    stroke(colorScheme.get(j + 1));
    beginShape();
    for (let i = 0; i <= N; i++) {
      const x = map(i, 0, N - 1, 0, width);
      // const y = map(series[j].data[i % 12], -10, 40, height, 0);

      const y = map(series[j].data[i % 12], -10, 40, 0, 1);
      const r = lerp(width / 4, width / 2, y);
      const theta0 = lerp(PI / 32, PI, twr);
      const theta = map(i, 0, N, -theta0, theta0) - PI / 2;
      const rx = r * cos(theta) * sx;
      const ry = r * sin(theta) + lerp(height / 4 * 1, 0, twr);
      curveVertex(rx, ry);
      if (i == 0 || i == N) {
        curveVertex(rx, ry);
      }
    }
    endShape();
  }
}
</script>