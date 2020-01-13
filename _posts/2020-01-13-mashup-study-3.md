---
layout: post
title:  "Mashup Study 3"
author: naoto
categories: [ sketch ]
tags: [ p5js, mashup ]
image: assets/images/2020-01-13-mashup-study-3.png
description: "A sketch"
featured: true
comments: true
p5: true
tonejs: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>


<script>
const width = 400;
const height = 400;

class Drawer {
  constructor({ f, args }) {
    this.f = f;
    if (args == undefined) {
      args = {}
    }
    if (args.col == undefined) {
      args.col = { bg: Math.floor(Math.random() * 5), fg: Math.floor(Math.random() * 5) };
      if (args.col.bg == args.col.fg) args.col.fg = (args.col.fg + 1) % 5;
    }
    if (args.sides == undefined) {
      args.sides = Math.floor(Math.random() * 5);
      args.r0 = Math.random();
      args.r1 = Math.random();
    }
    this.args = args;
  }
  draw({ pg }) {
    pg.push();
    if (this.args.wipe != undefined) {
      this.args.tw = myp5.constrain((t - this.args.wipe.bangT) / this.args.wipe.bangDur, 0, 1);
      this.args.bangParam = this.args.wipe.bangParam;
    }
    this.f(pg, this.args);
    pg.pop();
  }
}

class WipeGraphics {
  constructor({ p, pgF, pgB }) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height) : pgB;
    this.pgMask = p.createGraphics(width, height);
    this.pgMaskInv = p.createGraphics(width, height);
    this.pgbF = p.createGraphics(width, height);
    this.pgbB = p.createGraphics(width, height);

    this.bangT = 0;
    this.bangDur = 0.75;
    this.bangCycle = 0;
    this.bangParam = 0;
  }

  bang({ t, next, wipe }) {
    this.foreDraw = this.backDraw;
    this.backDraw = next;
    this.wipeDraw = wipe;
    this.bangT = t;
    this.bangCycle = (this.bangCycle + 1) % 2;
    this.bangParam = Math.floor(Math.random() * 4);
  }

  update() {
    let p = this.p;

    // setColorMode = 0;
    // this.backDraw.draw({ pg: this.pgB });
    // this.foreDraw.draw({ pg: this.pgF });
    // setColorMode = 2;
    // this.wipeDraw.draw({ pg: this.pgMask });
    // setColorMode = 0;

    this.pgbB.blendMode(p.BLEND);
    this.pgbB.background(0);
    this.pgbB.image(this.pgB, 0, 0);
    this.pgbB.blendMode(p.MULTIPLY);
    this.pgbB.image(this.pgMask, 0, 0);

    this.pgMaskInv.image(this.pgMask, 0, 0);
    this.pgMaskInv.filter(myp5.INVERT);

    this.pgbF.push();
    this.pgbF.blendMode(p.BLEND);
    this.pgbF.background(0);
    this.pgbF.image(this.pgF, 0, 0);
    this.pgbF.blendMode(p.MULTIPLY);
    this.pgbF.image(this.pgMaskInv, 0, 0);
  }

  draw({ pg }) {
    let p = this.p;

    pg.image(this.pgbB, 0, 0);
    pg.blendMode(p.ADD);
    pg.image(this.pgbF, 0, 0);
    pg.blendMode(p.BLEND);
  }
}

const wipeDraws = [
  (pg, args) => { // stripes
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam;
    let r = pg.width / 1.4 / n;
    pg.rectMode(myp5.CENTER);
    for (let i = -n; i <= n; i++) {
      pg.rect(i * r, 0, myp5.lerp(0, r, EasingFunctions.easeInOutCubic(tw)), pg.height * 2);
    }
  },
  (pg, args) => { // circle expand
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam;
    let r = myp5.lerp(0, pg.width * 1.42, EasingFunctions.easeInOutCubic(tw)) / (n * 2 + 1) * 2;
    for (let i = -n; i <= n; i++) {
      for (let j = -n; j <= n; j++) {
        pg.ellipse(pg.width / 2 / n * j, pg.width / 2 / n * i, r);
      }
    }
  },
  (pg, args) => { // shape expand
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam + 3;
    let r = myp5.lerp(0, pg.width * 1.42, EasingFunctions.easeInOutCubic(tw));
    pg.beginShape();
    for (let i = 0; i <= n; i++) {
      let theta = i / n * Math.PI * 2 - Math.PI / 2;
      let x = r * Math.cos(theta);
      let y = r * Math.sin(theta);
      pg.vertex(x, y);
    }
    pg.endShape(myp5.CLOSE);
  },
  (pg, args) => { // clock wipe
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let rate = EasingFunctions.easeInOutCubic(tw);
    let n = 128;
    let r = pg.width;
    pg.beginShape();
    pg.vertex(0, 0);
    let sign = bangParam % 2 == 0 ? -1 : 1;
    for (let i = 0; i <= n; i++) {
      let theta = sign * i / n * Math.PI * 2 * rate - Math.PI / 2;
      let x = r * Math.cos(theta);
      let y = r * Math.sin(theta);
      pg.vertex(x, y);
    }
    pg.vertex(0, 0);
    pg.endShape();
  },
  (pg, args) => { // horizontal box push
    let w = args.wipe;
    const tw = myp5.constrain((t - w.bangT) / w.bangDur, 0, 1);
    setColor(pg, 'background', 0);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(w.bangParam / 2 * Math.PI);
    pg.translate(-pg.width / 2, -pg.height / 2);
    let r = myp5.lerp(0, pg.width, EasingFunctions.easeInOutCubic(tw));
    pg.rect(0, 0, r, pg.height);
  },
];

WotC = {
  circleCnt: 0, vertexCnt: 0,
  MAX_CIRCLE_CNT: 500,
  MIN_CIRCLE_CNT: 100,
  MAX_VERTEX_CNT: 30,
  MIN_VERTEX_CNT: 3,

  getCenterByTheta: (theta, time, scale) => {
    let p = myp5;
    let direction = p.createVector(Math.cos(theta), Math.sin(theta));
    let distance = 0.6 + 0.2 * Math.cos(theta * 6.0 + Math.cos(theta * 8.0 + time));
    let circleCenter = direction.mult(distance * scale);
    return circleCenter;
  },

  getSizeByTheta: (theta, time, scale) => {
    let offset = 0.2 + 0.12 * Math.cos(theta * 9.0 - time * 2.0);
    let circleSize = scale * offset;
    return circleSize;
  },

  getColorByTheta: (theta, time) => {
    let p = myp5;
    let th = 8.0 * theta + time * 2.0;
    let r = 0.6 + 0.4 * Math.cos(th),
      g = 0.6 + 0.4 * Math.cos(th - Math.PI / 3),
      b = 0.6 + 0.4 * Math.cos(th - Math.PI * 2.0 / 3.0),
      alpha = p.map(WotC.circleCnt, WotC.MIN_CIRCLE_CNT, WotC.MAX_CIRCLE_CNT, 150, 100);
    return p.color(r * 255, g * 255, b * 255, alpha);
  }
}

PF = {
  circle: 200,
  rot: 0,
  col: 0,
  freq: 0.000005,
  cont: 0,
  r: 0
}


Blobs = {
  handle_len_rate: 3,
  maxDistance: 200,
  circlePaths: [],
  connections: [],

  setup: () => {
    //generate circles
    for (var i = 0; i < 10; i++) {
      Blobs.circlePaths.push({
        position: myp5.createVector(myp5.random(width), myp5.random(height)),
        radius: i == 0 ? 120 : myp5.random(100, 120),
        vel: myp5.createVector(myp5.random(-2, 2), myp5.random(-2, 2))
      });
    }
    Blobs.circlePaths[0].radius = 250*0.4;
  },

  metaball: (ball1, ball2, v, handle_len_rate, maxDistance) => {
    var radius1 = ball1.radius / 2;
    var radius2 = ball2.radius / 2;
    var center1 = ball1.position;
    var center2 = ball2.position;
    var d = center1.dist(center2);
    var u1 = 0;
    var u2 = 0;
    if (d > maxDistance || d <= Math.abs(radius1 - radius2)) {
      return;
    } else if (d < radius1 + radius2) {
      // case circles are overlapping
      u1 = Math.acos((radius1 * radius1 + d * d - radius2 * radius2) / (2 * radius1 * d));
      u2 = Math.acos((radius2 * radius2 + d * d - radius1 * radius1) / (2 * radius2 * d));
    }
    var angle1 = Math.atan2(center2.y - center1.y, center2.x - center1.x);
    var angle2 = Math.acos((radius1 - radius2) / d);
    var angle1a = angle1 + u1 + (angle2 - u1) * v;
    var angle1b = angle1 - u1 - (angle2 - u1) * v;
    var angle2a = angle1 + Math.PI - u2 - (Math.PI - u2 - angle2) * v;
    var angle2b = angle1 - Math.PI + u2 + (Math.PI - u2 - angle2) * v;
    var p1a = p5.Vector.add(center1, p5.Vector.fromAngle(angle1a, radius1));
    var p1b = p5.Vector.add(center1, p5.Vector.fromAngle(angle1b, radius1));
    var p2a = p5.Vector.add(center2, p5.Vector.fromAngle(angle2a, radius2));
    var p2b = p5.Vector.add(center2, p5.Vector.fromAngle(angle2b, radius2));
    // define handle length by the distance between
    // both ends of the curve to draw
    var d2 = Math.min(v * handle_len_rate, myp5.dist(p1a.x, p1a.y, p2a.x, p2a.y) / (radius1 + radius2));
    // case circles are overlapping:
    d2 *= Math.min(1, d * 2 / (radius1 + radius2));
    radius1 *= d2;
    radius2 *= d2;
    var path = {
      segments: [p1a, p2a, p2b, p1b],
      handles: [
        p5.Vector.fromAngle(angle1a - Math.PI/2, radius1),
        p5.Vector.fromAngle(angle2a + Math.PI/2, radius2),
        p5.Vector.fromAngle(angle2b - Math.PI/2, radius2),
        p5.Vector.fromAngle(angle1b + Math.PI/2, radius1)
      ]
    };
    return path;
  }
}

const solidDraws = [
  (pg, args) => {
    // Waltz of the Circles by MiniPear
    // https://www.openprocessing.org/sketch/748916
    let { r0, r1 } = args;
    let p = myp5;
    pg.push();
    pg.background(0);
    pg.translate(pg.width / 2, pg.height / 2);

    WotC.circleCnt = p.int(p.map(r0, 0, 1, WotC.MAX_CIRCLE_CNT, WotC.MIN_CIRCLE_CNT));
    WotC.vertexCnt = p.int(p.map(r1, 0, 1, WotC.MAX_VERTEX_CNT, WotC.MIN_VERTEX_CNT));

    for (let ci = 0; ci < WotC.circleCnt; ci++) {
      let time = p.frameCount / 20;
      let thetaC = p.map(ci, 0, WotC.circleCnt, 0, p.TAU);
      let scale = pg.width / 7 * 3;

      let circleCenter = WotC.getCenterByTheta(thetaC, time, scale);
      let circleSize = WotC.getSizeByTheta(thetaC, time, scale);
      let c = WotC.getColorByTheta(thetaC, time);

      pg.stroke(c);
      pg.noFill();
      if (WotC.vertexCnt > 20) {
        pg.ellipse(circleCenter.x, circleCenter.y, circleSize * 2);
      } else {
        pg.beginShape();
        for (let vi = 0; vi < WotC.vertexCnt; vi++) {
          let thetaV = p.map(vi, 0, WotC.vertexCnt, 0, p.TAU);
          let x = circleCenter.x + Math.cos(thetaV) * circleSize;
          let y = circleCenter.y + Math.sin(thetaV) * circleSize;
          pg.vertex(x, y);
        }
        pg.endShape(p.CLOSE);
      }
    }
    pg.pop();
  },
  (pg, args) => {
    // particleFlow by yasai
    // https://www.openprocessing.org/sketch/422446
    let p = myp5;
    pg.background(0);
    pg.translate(width / 2, height / 2);
    pg.rotate(p.radians(PF.rot));

    pg.ellipseMode(p.RADIUS);
    for (let i = 0; i < 500; i++) {
      PF.circle = width / 3 + width / 6 * 0.5 * Math.sin(p.millis() * PF.freq * i);
      PF.col = p.map(PF.circle, 150, 250, 255, 60);
      PF.r = p.map(PF.circle, 150 / 600 * width, 250 / 600 * width, 5, 2) / 600 * width;
      pg.fill(PF.col, 0, 74);
      pg.noStroke();
      pg.ellipse(PF.circle * Math.cos(i), PF.circle * Math.sin(i), PF.r, PF.r);
      PF.rot = PF.rot + 0.00005;
    }
  },
  (pg, args) => {
    // My Sketch by Mara@website.com
    // https://www.openprocessing.org/sketch/757327
    let p = myp5;
    pg.background(0);
    pg.fill(255);
    pg.noStroke();

    if (Blobs.circlePaths.length == 0) {
      Blobs.setup();
    }
    //draw circles
    Blobs.circlePaths.forEach((circle, index) => {
      with (circle) {
        if (index == 0 && false) {
          position.x = p.mouseX;
          position.y = p.mouseY;
        } else {
          position.add(vel);
          if (position.x > width) position.x = position.x - width;
          else if (position.x < 0) position.x = width - position.x;
          if (position.y > height) position.y = position.y - height;
          else if (position.y < 0) position.y = height - position.y;
        }
        pg.ellipse(position.x, position.y, radius, radius)
      }
    })

    //generate connections
    Blobs.connections.length = 0;
    for (var i = 0, l = Blobs.circlePaths.length; i < l; i++) {
      for (var j = i - 1; j >= 0; j--) {
        var path = Blobs.metaball(Blobs.circlePaths[i], Blobs.circlePaths[j], 0.5, Blobs.handle_len_rate, Blobs.maxDistance);
        if (path) {
          Blobs.connections.push(path);
        }
      }
    }

    //draw connections
    Blobs.connections.forEach(path => {
      pg.beginShape();
      for (var j = 0; j < 4; j++) {
        if (j == 0) pg.vertex(path.segments[j].x, path.segments[j].y);
        else if (j % 2 != 0) {
          pg.vertex(path.segments[(j + 1) % 4].x, path.segments[(j + 1) % 4].y);
        }
        if (j % 2 != 0) continue;
        pg.bezierVertex(
          path.segments[j].x + path.handles[j].x, path.segments[j].y + path.handles[j].y,
          path.segments[(j + 1) % 4].x + path.handles[(j + 1) % 4].x, path.segments[(j + 1) % 4].y + path.handles[(j + 1) % 4].y,
          path.segments[(j + 1) % 4].x, path.segments[(j + 1) % 4].y
        );
      }
      pg.endShape();
    })
  }
];

let t = 0;

let s = (p) => {
  let wipe0;

  p.setup = () => {
    p.createCanvas(400, 400);
    // p.createLoop({duration:1, framesPerSecond: 30, gif: { fileName: "instanceMode.gif" } })
    p.frameRate(30);

    wipe0 = new WipeGraphics({ p });
    wipe0.foreDraw = new Drawer({ f: solidDraws[0] });
    wipe0.backDraw = new Drawer({ f: solidDraws[1] });
    wipe0.wipeDraw = new Drawer({ f: solidDraws[2], args: { wipe: wipe0 } });
  }

  p.draw = () => {
    t = p.millis() * 0.001;

    wipe0.pgMask = blobCanvas;
    wipe0.pgF = noiseCanvas;
    wipe0.pgB = rainCanvas;
    wipe0.update();
    wipe0.draw({ pg: p });
  }

}

let myp5 = new p5(s, document.getElementById('p5sketch'));

let yoff = 0.0;
let blobCanvas;

s = function (p) {
  p.setup = function () {
    blobCanvas = p.createCanvas(400, 400).hide();
  }

  p.draw = function () {
    p.background(0);

    p.translate(p.width / 2, p.height / 2);

    let radius = 150;

    p.beginShape();
    let xoff = 0;
    for (let a = 0; a < p.TWO_PI; a += 0.1) {
      let offset = p.map(p.noise(xoff, yoff), 0, 1, -25, 25)*2;
      let r = radius + offset;
      let x = r * p.cos(a);
      let y = r * p.sin(a);
      p.vertex(x, y);
      xoff += 0.1;
      //ellipse(x, y, 4, 4);
    }
    p.endShape();

    yoff += 0.01;
  }

};

var p036 = new p5(s);

function Particle(sketch) {
  this.pos = sketch.createVector(sketch.random(sketch.width), sketch.random(sketch.height));
  this.vel = sketch.createVector(0, 0);
  this.acc = sketch.createVector(0, 0);
  this.maxspeed = 4;
  this.h = 0;

  this.prevPos = this.pos.copy();

  this.update = function() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxspeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }

  this.follow = function(vectors) {
    // print(vectors.length, x, y, cols, index, this.pos)
    var x = sketch.floor(this.pos.x / scl);
    var y = sketch.floor(this.pos.y / scl);
    var index = x + y * cols;
    var force = vectors[(index+cols*rows)%(cols*rows)];
    this.applyForce(force);
  }

  this.applyForce = function(force) {
    this.acc.add(force);
  }

  this.show = function() {
    sketch.stroke(this.h, 255, 255, 25);
    this.h = this.h + 1;
    if (this.h > 255) {
      this.h = 0;
    }
    sketch.strokeWeight(1);
    sketch.line(this.pos.x, this.pos.y, this.prevPos.x, this.prevPos.y);
    this.updatePrev();
  }

  this.updatePrev = function() {
    this.prevPos.x = this.pos.x;
    this.prevPos.y = this.pos.y;
  }

  this.edges = function() {
    if (this.pos.x > sketch.width) {
      this.pos.x = 0;
      this.updatePrev();
    }
    if (this.pos.x < 0) {
      this.pos.x = sketch.width;
      this.updatePrev();
    }
    if (this.pos.y > sketch.height) {
      this.pos.y = 0;
      this.updatePrev();
    }
    if (this.pos.y < 0) {
      this.pos.y = sketch.height;
      this.updatePrev();
    }

  }

}

let noiseCanvas;

var inc = 0.1;
var scl = 10;
var cols, rows;

var zoff = 0;

var fr;

var particles = [];

var flowfield;

s = function (sketch) {
  sketch.setup = function () {
    noiseCanvas = sketch.createCanvas(400, 400).hide();
    sketch.colorMode(sketch.HSB, 255);
    cols = sketch.floor(sketch.width / scl);
    rows = sketch.floor(sketch.height / scl);
    // fr = sketch.createP('');

    flowfield = new Array(cols * rows);

    for (var i = 0; i < 300; i++) {
      particles[i] = new Particle(sketch);
    }
    sketch.background(51);
  }

  sketch.draw = function () {
    var yoff = 0;
    for (var y = 0; y < rows; y++) {
      var xoff = 0;
      for (var x = 0; x < cols; x++) {
        var index = x + y * cols;
        var angle = sketch.noise(xoff, yoff, zoff) * sketch.TWO_PI * 4;
        var v = p5.Vector.fromAngle(angle);
        v.setMag(1);
        flowfield[index] = v;
        xoff += inc;
        sketch.stroke(0, 50);
        // push();
        // translate(x * scl, y * scl);
        // rotate(v.heading());
        // strokeWeight(1);
        // line(0, 0, scl, 0);
        // pop();
      }
      yoff += inc;

      zoff += 0.0003;
    }

    for (var i = 0; i < particles.length; i++) {
      particles[i].follow(flowfield);
      particles[i].update();
      particles[i].edges();
      particles[i].show();
    }

    // fr.html(floor(frameRate()));
  }

};

var p024 = new p5(s);

let walkCanvas;
var x;
var y;

s = function (p) {
  p.setup = function () {
    walkCanvas = p.createCanvas(400, 400).hide();
    x = 200;
    y = 200;
    p.background(51);
  }

  p.draw = function () {
    p.stroke(255, 100);
    p.strokeWeight(2);
    p.point(x, y);

    var r = p.floor(p.random(4));

    switch (r) {
      case 0:
        x = x + 1;
        break;
      case 1:
        x = x - 1;
        break;
      case 2:
        y = y + 1;
        break;
      case 3:
        y = y - 1;
        break;
    }


  }

};

// var p052 = new p5(s);


function Drop() {
  this.x = p004.random(p004.width);
  this.y = p004.random(-500, -50);
  this.z = p004.random(0, 20);
  this.len = p004.map(this.z, 0, 20, 10, 20);
  this.yspeed = p004.map(this.z, 0, 20, 1, 20);

  this.fall = function() {
    this.y = this.y + this.yspeed;
    var grav = p004.map(this.z, 0, 20, 0, 0.2);
    this.yspeed = this.yspeed + grav;

    if (this.y > p004.height) {
      this.y = p004.random(-200, -100);
      this.yspeed = p004.map(this.z, 0, 20, 4, 10);
    }
  }

  this.show = function() {
    var thick = p004.map(this.z, 0, 20, 1, 3);
    p004.strokeWeight(thick);
    p004.stroke(138, 43, 226);
    p004.line(this.x, this.y, this.x, this.y+this.len);
  }
}

let rainCanvas;

s = function (p) {

  var drops = [];

  p.setup = function () {
    rainCanvas = p.createCanvas(400, 400).hide();
    for (var i = 0; i < 500; i++) {
      drops[i] = new Drop();
    }
  }

  p.draw = function () {
    p.background(230, 230, 250);
    for (var i = 0; i < drops.length; i++) {
      drops[i].fall();
      drops[i].show();
    }
  }

};

var p004 = new p5(s);
</script>