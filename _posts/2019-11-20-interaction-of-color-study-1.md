---
layout: post
title:  "Interaction of Color Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, josefalbers ]
image: assets/images/2019-11-20-color.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<script>
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
const s = ( p ) => {
  p.setup = () => {
    const rect = document.getElementById('p5sketch').getBoundingClientRect();
    p.createCanvas(rect.width, rect.width);
  };
  p.draw = () => {
    p.background(0);
    setColor(p, 'background', 0);
    p.noStroke();
    const n = 4;
    const h = p.height / n;
    const t = p.millis() * 0.001;
    for(let i = 0; i < n; i++) {
      let tt = (t * 0.25) % 8;
      if(tt > 4) tt = 8 - tt;
      const deg = p.constrain(p.map(tt, i, i+1, 0, 1), 0, 1);
      p.push();
      if(i % 2 == 1) {
        p.translate(p.width / 2, 0);
        p.scale(-1, 1);
        p.translate(-p.width / 2, 0);
      }
      setColor(p, 'fill', i);
      p.rect(0, i * h, p.width, h);
      setColor(p, 'fill', (1 + i) % 4);
      const m = Math.pow(2, Math.floor(deg * 6) + 1);
      const w = p.width / m;
      for(let j = 0; j < m / 2; j++) {
        const subdeg = EasingFunctions.easeInOutCubic((deg * 6) % 1);
        let x = w * 2 * j;
        if(j % 2 == 1) {
          x = p.lerp(w * 2 * j - w, w * 2 * j, subdeg);
        }
        p.rect(x, i * h, w * 1, h);
      }
      p.pop();
    }
  };
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>