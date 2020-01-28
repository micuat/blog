---
layout: post
title:  "Pixel Weave Study 2"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2020-01-28-pixel-weave-study-2.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
const a = 100*100*4;
const b = 400*400*4-256;
const w = 400;
const d = 100;
const s = (p) => {
	let pg;
	p.setup = () => {
		p.createCanvas(w, w);
		// p.createLoop({duration:1, framesPerSecond: 30, gif: { fileName: "instanceMode.gif" } })
		p.pixelDensity(1);
		pg = p.createGraphics(w, w);
		pg.pixelDensity(1);
		pg.background(0, 0, 0, 255);
		pg.fill(255, 255, 0);
		pg.noStroke();
		for(let i = 0; i < 4; i++)
			pg.ellipse(i * 100, i * 100, 50, 50);
		p.image(pg, 0, 0);
		// p.frameRate(1)
	}

	p.draw = () => {
		// p.clear();
		pg.loadPixels();
		let pixels = pg.pixels;
		for (let x = 1; x < w * w * 4; x += w*2) {
			// pixels[x] *= (x/4) % w < d ? 0.5 : 2;
			// pixels.copyWithin(x - w - pixels[a] * 2, x + pixels[a], x + w-1);
			pixels.copyWithin(x - w*4-4*(pixels[x]>0?-1:1), x, x + w*4);
		}
		pixels.copyWithin(b-8e3, 1, 8e3);
		if (pixels[a-1] == 255) {
			pixels[a-1]=0;
			pixels.reverse();
		}
		pg.updatePixels();
		p.image(pg, 0, 0);
	}
}

// let myp5 = new p5(s)
let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>