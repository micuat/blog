---
layout: post
title:  "Geometry Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2020-03-05-geometry-study-1.gif
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

const cis = [];

class CI {
	constructor({
		r,
		x,
		y
	}) {
		this.r = r;
		this.R = r;
		this.x = x;
		this.y = y;
	}
	getIntersections(ci) {
		// http://shogo82148.github.io/homepage/memo/geometry/circle-cross.html
		const d01 = dist(this.x, this.y, ci.x, ci.y);
		const dx = ci.x - this.x;
		const dy = ci.y - this.y;
		const d2 = dx * dx + dy * dy;
		const a = (d2 + this.r * this.r - ci.r * ci.r) * 0.5;
		const sq = d2 * this.r * this.r - a * a;
		if (sq >= 0) {
			const x0 = ((a * dx) + dy * sqrt(sq)) / d2 + this.x;
			const y0 = ((a * dy) - dx * sqrt(sq)) / d2 + this.y;
			const x1 = ((a * dx) - dy * sqrt(sq)) / d2 + this.x;
			const y1 = ((a * dy) + dx * sqrt(sq)) / d2 + this.y;
			return [{
				x: x0,
				y: y0
			}, {
				x: x1,
				y: y1
			}];
		}
		return [];
	}
}

function setup() {
	createCanvas(400, 400).parent('p5sketch');
	// createLoop({duration:10, framesPerSecond: 15, gif:true})
	// pixelDensity(1);
	cis.push(new CI({
		r: 100,
		x: width / 2,
		y: height / 2
	}));
	for (let i = 0; i < 12; i++) {
		cis.push(new CI({
			r: random(50, width / 2),
			x: random(width),
			y: random(height)
		}));
	}
}

function draw() {
	const t = frameCount / 60;//millis() * 0.001;
	background(255);
	// cis[0].x = mouseX;
	// cis[0].y = mouseY;
	for (let i = 0; i < cis.length; i++) {
		cis[i].r = (Math.sin(t + i / cis.length * PI * 2) * 0.5 + 0.5) * cis[i].R;
	}

	fill(0);
	noStroke();
	for (let i = 0; i < cis.length; i++) {
		for (let j = i + 1; j < cis.length; j++) {
			const intersections = cis[i].getIntersections(cis[j]);
			for (const v of intersections) {
				ellipse(v.x, v.y, 10);
			}
		}
	}
	noFill();
	stroke(0);
	for (ci of cis) {
		ellipse(ci.x, ci.y, ci.r * 2); // diameter
	}
}
</script>