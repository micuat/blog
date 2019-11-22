---
layout: post
title:  "Yellowtail"
author: naoto
categories: [ sketch ]
tags: [ p5js, recode ]
image: assets/images/2019-03-20-Yellowtail.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Draw on the canvas to start.

Golan Levin's Yellowtail, ported to p5.js by Naoto Hieda.

<script>
//.parent("p5sketch")
/**
 * Yellowtail
 * by Golan Levin (www.flong.com). 
 * 
 * Click, drag, and release to create a kinetic gesture.
 * 
 * Yellowtail (1998-2000) is an interactive software system for the gestural 
 * creation and performance of real-time abstract animation. Yellowtail repeats 
 * a user's strokes end-over-end, enabling simultaneous specification of a 
 * line's shape and quality of movement. Each line repeats according to its 
 * own period, producing an ever-changing and responsive display of lively, 
 * worm-like textures.
 *
 * p5.js version by Naoto Hieda (naotohieda.com) 2019
 */

class Vec3f {
  constructor(ix, iy, ip) {
		if(ix != undefined)
	    set(ix, iy, ip);
		else
	    set(0, 0, 0);
  }

  set(ix, iy, ip) {
    this.x = ix;
    this.y = iy;
    this.p = ip;
  }
}

class Gesture {

  constructor(mw, mh) {
		this.damp = 5.0;
		this.dampInv = 1.0 / this.damp;
		this.damp1 = this.damp - 1;
		this.INIT_TH = 14;
		this.thickness = this.INIT_TH;

    this.w = mw;
    this.h = mh;
    this.capacity = 600;
    this.path = new Array(this.capacity);
    this.polygons = new Array(this.capacity);
    this.crosses  = new Array(this.capacity);
    for (let i=0;i<this.capacity;i++) {
      this.polygons[i] = new Polygon();
      this.path[i] = new Vec3f();
      this.crosses[i] = 0;
    }
    this.nPoints = 0;
    this.nPolys = 0;

    this.exists = false;
    this.jumpDx = 0;
    this.jumpDy = 0;
  }

  clear() {
    this.nPoints = 0;
    this.exists = false;
    this.thickness = this.INIT_TH;
  }

  clearPolys() {
    this.nPolys = 0;
  }

  addPoint(x, y) {

    if (this.nPoints >= this.capacity) {
      // there are all sorts of possible solutions here,
      // but for abject simplicity, I don't do anything.
    } 
    else {
      let v = this.distToLast(x, y);
      let p = this.getPressureFromVelocity(v);
      this.path[this.nPoints++].set(x,y,p);

      if (this.nPoints > 1) {
        this.exists = true;
        this.jumpDx = this.path[this.nPoints-1].x - this.path[0].x;
        this.jumpDy = this.path[this.nPoints-1].y - this.path[0].y;
      }
    }

  }

  getPressureFromVelocity(v) {
    const scale = 18;
    const minP = 0.02;
    const oldP = (this.nPoints > 0) ? this.path[this.nPoints-1].p : 0;
    return ((minP + max(0, 1.0 - v/scale)) + (this.damp1*oldP))*this.dampInv;
  }

  setPressures() {
    // pressures vary from 0...1
    let pressure;
    let tmp;
    let t = 0;
    let u = 1.0 / (this.nPoints - 1)*TWO_PI;
    for (let i = 0; i < this.nPoints; i++) {
      pressure = sqrt((1.0 - cos(t))*0.5);
      this.path[i].p = pressure;
      t += u;
    }
  }

  distToLast(ix, iy) {
    if (this.nPoints > 0) {
      let v = this.path[this.nPoints-1];
      let dx = v.x - ix;
      let dy = v.y - iy;
			
      return mag(dx, dy);
    } 
    else {
      return 30;
    }
  }

  compile() {
    // compute the polygons from the path of Vec3f's
    if (this.exists) {
      this.clearPolys();

      let p0, p1, p2;
      let radius0, radius1;
      let ax, bx, cx, dx;
      let ay, by, cy, dy;
      let   axi, bxi, cxi, dxi, axip, axid;
      let   ayi, byi, cyi, dyi, ayip, ayid;
      let p1x, p1y;
      let dx01, dy01, hp01, si01, co01;
      let dx02, dy02, hp02, si02, co02;
      let dx13, dy13, hp13, si13, co13;
      let taper = 1.0;

      let  nPathPoints = this.nPoints - 1;
      let  lastPolyIndex = nPathPoints - 1;
      let npm1finv =  1.0 / max(1, nPathPoints - 1);

      // handle the first point
      p0 = this.path[0];
      p1 = this.path[1];
      radius0 = p0.p * this.thickness;
      dx01 = p1.x - p0.x;
      dy01 = p1.y - p0.y;
      hp01 = sqrt(dx01*dx01 + dy01*dy01);
      if (hp01 == 0) {
        hp02 = 0.0001;
      }
      co01 = radius0 * dx01 / hp01;
      si01 = radius0 * dy01 / hp01;
      ax = p0.x - si01; 
      ay = p0.y + co01;
      bx = p0.x + si01; 
      by = p0.y - co01;

      let xpts;
      let ypts;

      let LC = 20;
      let RC = this.w-LC;
      let TC = 20;
      let BC = this.h-TC;
      let mint = 0.618;
      let tapow = 0.4;

      // handle the middle points
      let i = 1;
      let apoly;
      for (i = 1; i < nPathPoints; i++) {
        taper = pow((lastPolyIndex-i)*npm1finv,tapow);

        p0 = this.path[i-1];
        p1 = this.path[i  ];
        p2 = this.path[i+1];
        p1x = p1.x;
        p1y = p1.y;
        radius1 = Math.max(mint,taper*p1.p*this.thickness);

        // assumes all segments are roughly the same length...
        dx02 = p2.x - p0.x;
        dy02 = p2.y - p0.y;
        hp02 = Math.sqrt(dx02*dx02 + dy02*dy02);
        if (hp02 != 0) {
          hp02 = radius1/hp02;
        }
        co02 = dx02 * hp02;
        si02 = dy02 * hp02;

        // translate the integer coordinates to the viewing rectangle
        axi = axip = Math.floor(ax);
        ayi = ayip = Math.floor(ay);
        axi=(axi<0)?(this.w-((-axi)%this.w)):axi%this.w;
        axid = axi-axip;
        ayi=(ayi<0)?(this.h-((-ayi)%this.h)):ayi%this.h;
        ayid = ayi-ayip;

        // set the vertices of the polygon
        apoly = this.polygons[this.nPolys++];
        xpts = apoly.xpoints;
        ypts = apoly.ypoints;
        xpts[0] = axi = axid + axip;
        xpts[1] = bxi = axid + Math.floor(bx);
        xpts[2] = cxi = axid + Math.floor(cx = p1x + si02);
        xpts[3] = dxi = axid + Math.floor(dx = p1x - si02);
        ypts[0] = ayi = ayid + ayip;
        ypts[1] = byi = ayid + Math.floor(by);
        ypts[2] = cyi = ayid + Math.floor(cy = p1y - co02);
        ypts[3] = dyi = ayid + Math.floor(dy = p1y + co02);

        // keep a record of where we cross the edge of the screen
        this.crosses[i] = 0;
        if ((axi<=LC)||(bxi<=LC)||(cxi<=LC)||(dxi<=LC)) { 
          this.crosses[i]|=1; 
        }
        if ((axi>=RC)||(bxi>=RC)||(cxi>=RC)||(dxi>=RC)) { 
          this.crosses[i]|=2; 
        }
        if ((ayi<=TC)||(byi<=TC)||(cyi<=TC)||(dyi<=TC)) { 
          this.crosses[i]|=4; 
        }
        if ((ayi>=BC)||(byi>=BC)||(cyi>=BC)||(dyi>=BC)) { 
          this.crosses[i]|=8; 
        }

        //swap data for next time
        ax = dx; 
        ay = dy;
        bx = cx; 
        by = cy;
      }

      // handle the last point
      p2 = this.path[nPathPoints];
      apoly = this.polygons[this.nPolys++];
      xpts = apoly.xpoints;
      ypts = apoly.ypoints;

      xpts[0] = Math.floor(ax);
      xpts[1] = Math.floor(bx);
      xpts[2] = Math.floor(p2.x);
      xpts[3] = Math.floor(p2.x);

      ypts[0] = Math.floor(ay);
      ypts[1] = Math.floor(by);
      ypts[2] = Math.floor(p2.y);
      ypts[3] = Math.floor(p2.y);

    }
  }

  smooth() {
    // average neighboring points

    const weight = 18;
    const scale  = 1.0 / (weight + 2);
    let nPointsMinusTwo = this.nPoints - 2;
    let lower, upper, center;

    for (let i = 1; i < nPointsMinusTwo; i++) {
      lower = this.path[i-1];
      center = this.path[i];
      upper = this.path[i+1];

      center.x = (lower.x + weight*center.x + upper.x)*scale;
      center.y = (lower.y + weight*center.y + upper.y)*scale;
    }
  }
}

class Polygon {
  xpoints = new Array(4);
  ypoints = new Array(4);
}

let gestureArray;
const nGestures = 36;  // Number of gestures
const minMove = 3;     // Minimum travel for a new point
let currentGestureID;

let tempP;
let tmpXp;
let tmpYp;


function setup () {
	createCanvas(400, 400).parent("p5sketch");
	
  background(0, 0, 0);
  noStroke();

  currentGestureID = -1;
  gestureArray = new Array(nGestures);
  for (let i = 0; i < nGestures; i++) {
    gestureArray[i] = new Gesture(width, height);
  }
  clearGestures();
}

function draw () {
  background(0);

  updateGeometry();
  fill(255, 255, 245);
  for (let i = 0; i < nGestures; i++) {
    renderGesture(gestureArray[i], width, height);
  }
}

function mousePressed () {
  currentGestureID = (currentGestureID+1) % nGestures;
  let G = gestureArray[currentGestureID];
  G.clear();
  G.clearPolys();
  G.addPoint(mouseX, mouseY);
}

function mouseDragged () {
  if (currentGestureID >= 0) {
    let G = gestureArray[currentGestureID];
    if (G.distToLast(mouseX, mouseY) > minMove) {
      G.addPoint(mouseX, mouseY);
      G.smooth();
      G.compile();
    }
  }
}

function keyPressed () {
  if (key == '+' || key == '=') {
    if (currentGestureID >= 0) {
      let th = gestureArray[currentGestureID].thickness;
      gestureArray[currentGestureID].thickness = min(96, th+1);
      gestureArray[currentGestureID].compile();
    }
  } else if (key == '-') {
    if (currentGestureID >= 0) {
      let th = gestureArray[currentGestureID].thickness;
      gestureArray[currentGestureID].thickness = max(2, th-1);
      gestureArray[currentGestureID].compile();
    }
  } else if (key == ' ') {
    clearGestures();
  }
}

function renderGesture (gesture, w, h) {
  if (gesture.exists) {
    if (gesture.nPolys > 0) {
      let polygons = gesture.polygons;
      let crosses = gesture.crosses;

      let xpts;
      let ypts;
      let p;
      let cr;

      beginShape(QUADS);
      let gnp = gesture.nPolys;
      for (let i=0; i<gnp; i++) {

        p = polygons[i];
        xpts = p.xpoints;
        ypts = p.ypoints;

        vertex(xpts[0], ypts[0]);
        vertex(xpts[1], ypts[1]);
        vertex(xpts[2], ypts[2]);
        vertex(xpts[3], ypts[3]);

        if ((cr = crosses[i]) > 0) {
          if ((cr & 3)>0) {
            vertex(xpts[0]+w, ypts[0]);
            vertex(xpts[1]+w, ypts[1]);
            vertex(xpts[2]+w, ypts[2]);
            vertex(xpts[3]+w, ypts[3]);

            vertex(xpts[0]-w, ypts[0]);
            vertex(xpts[1]-w, ypts[1]);
            vertex(xpts[2]-w, ypts[2]);
            vertex(xpts[3]-w, ypts[3]);
          }
          if ((cr & 12)>0) {
            vertex(xpts[0], ypts[0]+h);
            vertex(xpts[1], ypts[1]+h);
            vertex(xpts[2], ypts[2]+h);
            vertex(xpts[3], ypts[3]+h);

            vertex(xpts[0], ypts[0]-h);
            vertex(xpts[1], ypts[1]-h);
            vertex(xpts[2], ypts[2]-h);
            vertex(xpts[3], ypts[3]-h);
          }

          // I have knowingly retained the small flaw of not
          // completely dealing with the corner conditions
          // (the case in which both of the above are true).
        }
      }
      endShape();
    }
  }
}

function updateGeometry () {
  let J;
  for (let g=0; g<nGestures; g++) {
    if ((J=gestureArray[g]).exists) {
      if (g!=currentGestureID) {
        advanceGesture(J);
      } else if (!mouseIsPressed) {
        advanceGesture(J);
      }
    }
  }
}

function advanceGesture (gesture) {
  // Move a Gesture one step
  if (gesture.exists) { // check
    let nPts = gesture.nPoints;
    let nPts1 = nPts-1;
    let path;
    let jx = gesture.jumpDx;
    let jy = gesture.jumpDy;

    if (nPts > 0) {
      path = gesture.path;
      for (let i = nPts1; i > 0; i--) {
        path[i].x = path[i-1].x;
        path[i].y = path[i-1].y;
      }
      path[0].x = path[nPts1].x - jx;
      path[0].y = path[nPts1].y - jy;
      gesture.compile();
    }
  }
}

function clearGestures () {
	
  for (let i = 0; i < nGestures; i++) {
    gestureArray[i].clear();
  }
}
</script>