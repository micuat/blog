---
layout: post
title:  "Kasuri Study 1"
author: naoto
categories: [ sketch ]
image: assets/images/2019-08-25-Kasuri-Study.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
//.parent("p5sketch")
const data = {};

function preload() {
  data.map = loadImage('{{ site.baseurl }}/assets/images/2019-08-25-brain-map.png');
}

function createPalette(_url) {
  let slash_index = _url.lastIndexOf('/');
  let palatte_str = _url.slice(slash_index + 1);
  let arr = palatte_str.split('-');
  for (let i = 0; i < arr.length; i++) {
    arr[i] = '#' + arr[i];
  }
  return arr;
}

function setup() {
  createCanvas(400, 400).parent("p5sketch");
  const pal = createPalette('https://coolors.co/476a6f-519e8a-7eb09b-c5c9a4-ecbeb4');

  background(0);
  data.map.loadPixels();
  //image(data.map, 0, 0);
  const w = 1;
  const rs = 15;
  const ra = 50;
  for (let i = 0; i < height; i += w) {
    const shift = floor(random(-rs, rs));
    for (let j = 0; j < width; j += w) {
      if (((i / w) % 2 == 0 && (j / w) % 2 == 0) || ((i / w) % 2 == 1 && (j / w) % 2 == 1)) {

        const a = (data.map.pixels[((j * 2 + shift) + (i * 2) * data.map.width) * 4]) + random(-ra, ra);
        for (let I = 0; I < w; I++) {
          for (let J = 0; J < w; J++) {
            // if (a > 128)
            //   set(j + J, i + I, color(pal[0]));
            // else
            //   set(j + J, i + I, color(pal[2]));
            set(j + J, i + I, lerpColor(color(pal[2]), color(pal[0]), a/255));
          }
        }
      }

    }
  }

  for (let j = 0; j < width; j += w) {
    const shift = floor(random(-rs, rs));
    for (let i = 0; i < height; i += w) {
      if (((i / w) % 2 == 1 && (j / w) % 2 == 0) || ((i / w) % 2 == 0 && (j / w) % 2 == 1)) {

        const a = (data.map.pixels[((j * 2) + max(0, i * 2 + shift) * data.map.width) * 4]) + random(-ra, ra);
        for (let I = 0; I < w; I++) {
          for (let J = 0; J < w; J++) {
            set(j + J, i + I, lerpColor(color(pal[4]), color(pal[1]), a/255));
          }
        }
      }
    }
  }
  updatePixels();
  noLoop();
}

function draw() {}
</script>