---
layout: post
title:  "Interaction of Color Study 3"
author: naoto
categories: [ sketch ]
tags: [ p5js, josefalbers ]
image: assets/images/2020-04-25-interaction-of-color-study-3.png
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
        this.colors.push(color('#' + cs[i]));
      }
    }
  }
  get(i) {
    return this.colors[i];
  }
	lerp(r) {
		const i = Math.floor(Math.max(0, r)) % 5;
		const c0 = this.get(i);
		const c1 = this.get((i + 1) % 5);
		return lerpColor(c0, c1, r % 1);
	}
}

const everyFluctuation = 120;
const everyTarget = 180;

class Tile {
	constructor({i, j, N, M, palette, prev}) {
		this.i = i;
		this.j = j;
		this.N = N;
		this.M = M;
		this.palette = palette;
		this.prev = prev;
		this.rate = 0;
		this.r = 0;
		this.origin = 0;
		this.target = 0;
	}
	draw(pg) {
		const w = pg.width / this.N;
		const h = pg.height / this.M;
		const x = this.i * w;
		// const count = Math.floor(frameCount / 30);
		const y = this.j * h;
		// let rate = this.j / (this.M - 1);
		// if(this.i % 2 == 0) rate = 1 - rate;
		// const co = this.palette.lerp(this.i + rate);
		if (this.prev != undefined && this.prev.r >= 5) {
			if (this.prev.target != this.target) {
				this.origin = this.rate;
				this.target = this.prev.target;
				this.r = 0;
			}
		}
		this.rate = map(this.r, 0, everyTarget, this.origin, this.target);
		this.r++;

		const co = this.palette.lerp(this.rate);
		pg.fill(co);
		// pg.fill(this.rate * 255 / 5);
		pg.rect(x, y, w, h);

	}
}

const tiles = [];

function setup() {
	let palette = new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f");
	createCanvas(400, 400);
	background(100);
	const N = 5;
	const M = 10;
	let prev;
	for(let i = 0; i < N; i++) {
		for(let j = 0; j < M; j++) {
			const tile = new Tile({i, j: i % 2 == 0 ? j : M - 1 - j, N, M, palette, prev});
			tiles.push(tile);
			prev = tile;
		}
	}
	// tiles[0].prev = prev;
	tiles[0].target = 5;
}

function draw() {
	noStroke();
	// if (frameCount % 480 == 0) {
	// 	tiles[0].target = 5;
	// 	tiles[0].r = 0;
	// }
	// if (frameCount % 480 == 240) {
	// 	tiles[0].target = 0;
	// 	tiles[0].r = 0;
	// }
	if (frameCount % everyFluctuation == 0) {
		tiles[0].origin = tiles[0].rate;
		tiles[0].target = Math.floor(Math.random() * 6);
		tiles[0].r = 0;
	}
	for (const tile of tiles) {
		tile.draw(this);
	}
}

handler = setInterval(()=>{
  const canvas = document.getElementById("defaultCanvas0");
  if(canvas != undefined) {
    clearInterval(handler);
    document.getElementById("p5sketch").appendChild(canvas);
  }
}, 500);
</script>