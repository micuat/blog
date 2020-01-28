---
layout: post
title:  "Pixel Weave Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2020-01-27-pixel-weave-study-1.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
const a = 15e4;
const w = 200;
const d = 100;
const s = (p) => {
	let pg;
	p.setup = () => {
		p.createCanvas(w, w);
		p.pixelDensity(1);
		pg = p.createGraphics(w, w);
		pg.pixelDensity(1);
		pg.background(0, 0, 0, 255);
		pg.fill(255, 255, 0);
		pg.noStroke();
		pg.rect(0, 50, 50, 100);
		pg.fill(0, 0, 255);
		pg.ellipse(0, 50, 50, 50);
		p.image(pg, 0, 0);
		//createLoop({duration:20, framesPerSecond: 30, gif:true})
		// p.frameRate(1)
	}

	p.draw = () => {
		// p.clear();
		pg.loadPixels();
		let pixels = pg.pixels;
		for (let x = 3; x < w * w * 4; x += 9 + pixels[a]) {
			pixels[x] *= (x/4) % w < d ? 0.5 : 2;
			pixels.copyWithin(x - w - pixels[a] * 2, x + pixels[a], x + w);
		}
		pixels.copyWithin(a, 1, 2e3);
		if (pixels[a - 1] > d) {
			if (pixels[8e4] % 2 == 0) {
				pixels.reverse();
			}
			else if (pixels[8e4] % 16 == 9) {
				pixels.sort();
			}
		}
		pg.updatePixels();
		p.image(pg, 0, 0);
	}
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>