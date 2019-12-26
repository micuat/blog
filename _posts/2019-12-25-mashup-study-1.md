---
layout: post
title:  "Mashup Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, mashup ]
image: assets/images/2019-12-25-mashup-study-1.png
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
function midiToFreq(m) {
  let tuning = 440;
  return Math.pow(2, (m - 69) / 12) * tuning;
}

let setColorMode = 0;

class ColorScheme {
  constructor(colorString) {
    this.colors = []; {
      let cc = colorString.split("/");
      let cs = cc[cc.length - 1].split("-");
      for (let i in cs) {
        let r = parseInt("0x" + cs[i].substring(0, 2));
        let g = parseInt("0x" + cs[i].substring(2, 4));
        let b = parseInt("0x" + cs[i].substring(4, 6));
        this.colors.push({
          r: r,
          g: g,
          b: b
        });
      }
      this.offset = 0;
    }
  }
  get(i) {
    i = Math.min(this.colors.length - 1, Math.max(0, i));
    return this.colors[(i + this.offset) % this.colors.length];
  }

}

var colorSchemes = [
  new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f"),
  new ColorScheme("https://coolors.co/7fc29b-b5ef8a-5386e4-817e9f-d7f171"),
  new ColorScheme("https://coolors.co/000000-808080-ffffff-333333-aaaaaa"),
  new ColorScheme("https://coolors.co/ffffff-808080-000000-333333-aaaaaa"),
];

function setColor(parent, func, index, alpha) {
  let idx = setColorMode;
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[idx].get(index).r, colorSchemes[idx].get(index).g, colorSchemes[idx].get(index).b, alpha);
}

EasingFunctions = {
  // no easing, no acceleration
  linear: function(t) {
    return t
  },
  // accelerating from zero velocity
  easeInQuad: function(t) {
    return t * t
  },
  // decelerating to zero velocity
  easeOutQuad: function(t) {
    return t * (2 - t)
  },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function(t) {
    return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t
  },
  // accelerating from zero velocity 
  easeInCubic: function(t) {
    return t * t * t
  },
  // decelerating to zero velocity 
  easeOutCubic: function(t) {
    return (--t) * t * t + 1
  },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function(t) {
    return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1
  },
  // accelerating from zero velocity 
  easeInQuart: function(t) {
    return t * t * t * t
  },
  // decelerating to zero velocity 
  easeOutQuart: function(t) {
    return 1 - (--t) * t * t * t
  },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function(t) {
    return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t
  },
  // accelerating from zero velocity
  easeInQuint: function(t) {
    return t * t * t * t * t
  },
  // decelerating to zero velocity
  easeOutQuint: function(t) {
    return 1 + (--t) * t * t * t * t
  },
  // acceleration until halfway, then deceleration 
  easeInOutQuint: function(t) {
    return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t
  }
}

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

class CutGraphics {
  constructor({ p, pgF, pgB }) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height) : pgB;

    this.bangT = 0;
    this.bangDur = 0.75;
    this.bangParam = 0;
  }

  bang({ t, next }) {
    this.foreDraw = this.backDraw;
    this.backDraw = next;
    this.bangT = t;
    this.bangParam = Math.floor(Math.random() * 4);
  }

  update() {
    let p = this.p;

    this.backDraw.draw({ pg: this.pgB });
    this.foreDraw.draw({ pg: this.pgF });
  }

  draw({ pg }) {
    let p = this.p;

    let tw = p.constrain((t - this.bangT) / this.bangDur, 0, 1);
    pg.image(this.pgB, 0, 0);
    pg.push();
    pg.imageMode(p.CENTER);
    pg.translate(width / 2, height / 2);
    pg.rotate(this.bangParam * Math.PI / 2);
    pg.translate(-EasingFunctions.easeInOutCubic(tw) * width, 0);
    pg.rotate(-this.bangParam * Math.PI / 2);
    pg.image(this.pgF, 0, 0);
    pg.pop();
  }
}

class WipeGraphics {
  constructor({ p, pgF, pgB }) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height) : pgB;
    this.pgMask = p.createGraphics(width, height);
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

    setColorMode = 0;
    this.backDraw.draw({ pg: this.pgB });
    this.foreDraw.draw({ pg: this.pgF });
    setColorMode = 2;
    this.wipeDraw.draw({ pg: this.pgMask });
    setColorMode = 0;

    this.pgbB.blendMode(p.BLEND);
    this.pgbB.background(0);
    this.pgbB.image(this.pgB, 0, 0);
    this.pgbB.blendMode(p.MULTIPLY);
    this.pgbB.image(this.pgMask, 0, 0);

    this.pgMask.filter(myp5.INVERT);

    this.pgbF.push();
    this.pgbF.blendMode(p.BLEND);
    this.pgbF.background(0);
    this.pgbF.image(this.pgF, 0, 0);
    this.pgbF.blendMode(p.MULTIPLY);
    this.pgbF.image(this.pgMask, 0, 0);
  }

  draw({ pg }) {
    let p = this.p;

    pg.image(this.pgbB, 0, 0);
    pg.blendMode(p.ADD);
    pg.image(this.pgbF, 0, 0);
    pg.blendMode(p.BLEND);
  }
}

class GlitchGraphics {
  constructor({ p, pgF, }) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.buf = [];
    for (let i = 0; i < 10; i++) {
      let h = Math.random() * height / 32;
      let y = Math.random() * (height - h);
      let dx = (Math.random() - 0.5) * width / 12;
      let dy = (Math.random() - 0.5) * h;
      this.buf.push({ h, y, dx, dy });
    }
  }

  draw({ pg }) {
    let p = this.p;

    pg.image(this.pgF, 0, 0);
    for (let i = 0; i < 10; i++) {
      let { h, y, dx, dy } = this.buf[i];
      pg.image(this.pgF, dx, y + dy, width, h,
        0, y, width, h)
    }
    let h = Math.random() * height / 32;
    let y = Math.random() * (height - h);
    let dx = (Math.random() - 0.5) * width / 12;
    let dy = (Math.random() - 0.5) * h;
    this.buf.push({ h, y, dx, dy });
    this.buf.shift();
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
  // (pg, args) => {
  //   let { col, sides } = args;
  //   setColor(pg, 'background', col.bg);
  //   setColor(pg, 'fill', col.fg);
  //   pg.noStroke();
  //   let n = sides + 1;
  //   let r = pg.width / n / 4;
  //   pg.translate(pg.width / 2, pg.height / 2);
  //   for (let i = -n; i <= n; i++) {
  //     for (let j = -n; j <= n; j++) {
  //       pg.push();
  //       pg.translate(pg.width / 2 / n * j, pg.width / 2 / n * i);
  //       pg.ellipse(0, 0, r, r);
  //       pg.pop();
  //     }
  //   }
  // },
  // (pg, args) => {
  //   let { col, sides } = args;
  //   let n = sides + 1;
  //   let r = pg.width / n / 4 * Math.sqrt(2);

  //   if ((t + 1) % 4 < 2) {
  //     setColor(pg, 'background', col.bg);
  //     setColor(pg, 'fill', col.fg);
  //   }
  //   else {
  //     setColor(pg, 'background', col.fg);
  //     setColor(pg, 'fill', col.bg);
  //     pg.translate(pg.width / 2 / n * 0.5, pg.width / 2 / n * 0.5);
  //   }
  //   pg.noStroke();
  //   pg.rectMode(myp5.CENTER);
  //   pg.translate(pg.width / 2, pg.height / 2);
  //   for (let i = -n; i <= n; i++) {
  //     for (let j = -n; j <= n; j++) {
  //       pg.push();
  //       pg.translate(pg.width / 2 / n * j, pg.width / 2 / n * i);
  //       pg.rotate((EasingFunctions.easeInOutQuint(t % 1) + Math.floor(t)) / 4 * Math.PI);
  //       pg.rect(0, 0, r, r);
  //       pg.pop();
  //     }
  //   }
  // },
];

let t = 0;

const s = (p) => {
  const synths = {};

  let freq = 440;
  let pointer = 0;
  let codeInput;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = '<<<<<40f<<mmmm><====>>86~';
  let pastCommands = [];

  let wipe0, cut0;
  let glitch0;
  let turn;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    wipe0 = new WipeGraphics({ p });
    wipe0.foreDraw = new Drawer({ f: solidDraws[0] });
    wipe0.backDraw = new Drawer({ f: solidDraws[1] });
    wipe0.wipeDraw = new Drawer({ f: solidDraws[2], args: { wipe: wipe0 } });

    cut0 = new CutGraphics({ p });
    cut0.foreDraw = new Drawer({ f: solidDraws[2] });
    cut0.backDraw = new Drawer({ f: solidDraws[1] });

    turn = wipe0;

    glitch0 = new GlitchGraphics({ p });

    synths['~'] = new Tone.Synth({
      oscillator: { type: 'triangle' }
    }).toMaster();
    synths['a'] = new Tone.AMSynth().toMaster();
    synths['f'] = new Tone.FMSynth().toMaster();
    synths['N'] = new Tone.Synth({
      oscillator: { type: 'sawtooth' }
    }).toMaster();
    synths['^'] = new Tone.Synth({
      oscillator: { type: 'triangle' }
    }).toMaster();
    synths['['] = new Tone.Synth({
      oscillator: { type: 'square' }
    }).toMaster();
    synths['p'] = new Tone.PluckSynth().toMaster();
    synths['m'] = new Tone.MetalSynth().toMaster();
    synths['n'] = new Tone.NoiseSynth().toMaster();

    // codeInput = p.createInput(codeBase);
    // codeInput.size(p.width);

    // codeInput.elt.onkeyup = runButtonClicked;
  }

  p.mouseClicked = () => {
    // runButtonClicked();
  }

  let node;
  let lastNode;
  let curSynth;
  let curPattern = 0;

  p.draw = () => {
    t = p.millis() * 0.001;

    if (isPlaying) {
      if (pointer < tokens.length) {
        lastNode = node;
        node = tokens[pointer];
        execute(node);
        if (node == '~') {
          curPattern = (curPattern + 1) % wipeDraws.length;
          turn = p.random([wipe0, cut0]);
          next = new Drawer({ f: p.random(solidDraws) })
          wipe0.bang({
            t, next,
            wipe: new Drawer({
              f: wipeDraws[curPattern],
              args: { wipe: wipe0 }
            })
          });
          cut0.bang({
            t, next
          });
        }
      } else {
        isPlaying = false;
      }
    } else {
      for (const key in synths) {
        synths[key].triggerRelease();
      }
      prevChar = '';
    }

    if (!isNaN(node)) {
      pastCommands.push(freq);
    } else {
      pastCommands.push(node);
    }
    if (pastCommands.length > 15 * 15) pastCommands.shift();
    pointer++;

    turn.update();
    if (curSynth == 'n' || curSynth == 'm') {
      turn.draw({ pg: glitch0.pgF });
      glitch0.draw({ pg: p });
    }
    else {
      turn.draw({ pg: p });
    }
  }

  let runButtonClicked = () => {
    isPlaying = true;

    let code = codeInput.value();
    let unbalancedBrackets = (code.split("<").length - 1) - (code.split(">").length - 1);
    if (unbalancedBrackets > 0) {
      code += '>'.repeat(unbalancedBrackets);
    }
    code = unpack(code);

    while (code.indexOf('<') > -1) {
      code = unpack(code);
    }

    let lex = code.match(/(\D+)|[+-]?(\d*[.])?\d+/gi);
    parse(lex);
  }

  let unpack = (code, index) => {
    let pointer = 0;
    let result = '';
    let start = 0;
    let end = 0;
    let stack = 0;

    let peek = () => {
      return code[pointer];
    }

    let consume = () => {
      pointer++;
    }

    while (pointer < code.length) {
      let t = peek();
      if (t === "<") {
        if (stack == 0) {
          start = pointer;
        }
        stack++;
      } else if (t === ">") {
        end = pointer;
        stack--;
        if (stack == 0) {
          result += code.slice(start + 1, end).repeat(2);
        }
      } else {
        if (stack == 0) {
          result += t;
        }
      }
      consume();
    }

    return result;
  }


  let parse = (l) => {
    pointer = 0;
    tokens = [];
    if (l) {
      for (let i = 0; i < l.length; i++) {
        if (isNaN(l[i])) {
          let chars = l[i].split('');
          for (let j = 0; j < chars.length; j++) {
            tokens.push(chars[j]);
          }
        } else {
          tokens.push(l[i]);
        }
      }
    }
  }

  let execute = (t) => {
    if (t != prevChar) {
      if (isNaN(t)) {
        switch (t) {
          case '~':
          case 'a':
          case 'f':
          case 'N':
          case '^':
          case '[':
          case 'p':
          case 'm':
            curSynth = t;
            if (t == 'm') {
              synths[t].triggerAttack();
            } else {
              synths[t].triggerAttack(midiToFreq(freq));
            }
            for (const key in synths) {
              if (key != t) {
                synths[key].triggerRelease();
              }
            }
            break;
          case '=':
            curSynth = '';
            for (const key in synths) {
              synths[key].triggerRelease();
            }
            break;
          case '+':
          case '-':
          case '*':
          case '/':
          case '<':
          case '>':
            break;
          default:
            curSynth = 'n';
            synths.n.triggerAttack();
        }
      } else {
        if (prevChar == "+") {
          freq += parseFloat(t);
        } else if (prevChar == "-") {
          freq -= parseFloat(t);
        } else if (prevChar == "*") {
          freq *= parseFloat(t);
        } else if (prevChar == "/") {
          freq /= parseFloat(t);
        } else {
          freq = parseFloat(t);
        }

        // if (freq == 0) freq = p.random(110);

        let f = midiToFreq(freq);
        if (isNaN(f) == false && f < 1e5) {
          for (const key in synths) {
            if (key != 'n' && key != 'p') {
              synths[key].frequency.setValueAtTime(f);
            }
          }
        }
      }
    }
    prevChar = t;
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>