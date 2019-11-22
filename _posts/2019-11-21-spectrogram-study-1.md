---
layout: post
title:  "Spectrogram Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-11-21-spectrogram.png
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
  new ColorScheme("https://coolors.co/a6cfd5-7f96ff-e56399-320e3b-dbfcff"),
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
  let mic, fft;
  let count = 0;
  p.setup = () => {
    const rect = document.getElementById('p5sketch').getBoundingClientRect();
    p.createCanvas(rect.width, rect.width);
    mic = new p5.AudioIn();
    mic.start();
    fft = new p5.FFT();
    fft.setInput(mic);
    setColor(p, 'background', 0);
  };
  p.draw = () => {
    p.noFill();
    let spectrum = fft.analyze();
    // p.beginShape();
    const c0 = p.color(colorSchemes[0].get(0).r, colorSchemes[0].get(0).g, colorSchemes[0].get(0).b);
    const c1 = p.color(colorSchemes[0].get(1).r, colorSchemes[0].get(1).g, colorSchemes[0].get(1).b);
    const c2 = p.color(colorSchemes[0].get(2).r, colorSchemes[0].get(2).g, colorSchemes[0].get(2).b);
    for (i = 0; i < spectrum.length; i++) {
      // setColor(p, 'stroke', i % 5);
      let a = spectrum[i] / 255;
      if(a < 0.5) {
        p.stroke(p.lerpColor(c0, c1, a * 2));
      }
      else {
        p.stroke(p.lerpColor(c1, c2, a * 2 - 1));
      }
      let x = count;
      let y = p.height - i;
      p.line(x, y, x, y + 1);
    }
    // p.endShape();
    count = (count + 1) % p.width;
  };
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>