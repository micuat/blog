---
layout: post
title:  "Interaction of Color Study 2"
author: naoto
categories: [ sketch ]
tags: [ p5js, josefalbers ]
image: assets/images/2020-04-24-interaction-of-color-study-2.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<script>
class ColorScheme {
  constructor(colorString) {
    this.colors = []; {
      let cc = colorString.split("/");
      let cs = cc[cc.length - 1].split("-");
      for (let i in cs) {
        this.colors.push('#' + cs[i]);
      }
    }
  }
  get(i) {
    return this.colors[i];
  }

}

var colorScheme = new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f");

function setup() {
	createCanvas(400, 400);
	background(100);
}

function draw() {
	noStroke();
	const N = 5;
	const M = 10;
	const w = width / N;
	const h = height / M;
	for(let i = 0; i < N; i++) {
		const x = i * w;
		const c0 = color(colorScheme.get(i));
		const c1 = color(colorScheme.get((i + 1) % 5));
		for(let j = 0; j < M; j++) {
			const y = h * j;
			let rate = j / (M - 1);
			if(i % 2 == 0) rate = 1 - rate;
			const co = lerpColor(c0, c1, rate);
			fill(co);
			rect(x, y, w, h);
		}
	}
	noLoop();
}

handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>