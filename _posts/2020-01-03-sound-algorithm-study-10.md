---
layout: post
title:  "Sound Algorithm Study 10"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2020-01-03-sound-algorithm-study-10.png
description: "A sketch"
featured: true
comments: true
p5: true
tonejs: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start.

* [My Sketch by Mara@website.com](https://www.openprocessing.org/sketch/757327)

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
  new ColorScheme("https://coolors.co/102542-f87060-cdd7d6-b3a394-ffffff"),
  new ColorScheme("https://coolors.co/ffffff-102542-f87060-cdd7d6-b3a394"),
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
  constructor({ c, args }) {
    this.c = c;
    if (args == undefined) {
      args = {};
    }
    if (args.col == undefined) {
      args.col = { bg: Math.floor(Math.random() * 5), fg: Math.floor(Math.random() * 5) };
      if (args.col.bg == args.col.fg) args.col.fg = (args.col.fg + 2) % 5;
    }
    if (args.sides == undefined) {
      args.sides = Math.floor(Math.random() * 5);
      args.r0 = Math.random();
      args.r1 = Math.random();
    }
    this.args = args;
  }
  draw({ pg, args }) {
    pg.push();
    if (typeof this.c === 'object') {
      this.c.draw(pg, { ...this.args, ...args });
    }
    pg.pop();
  }
}

class LayerGraphics {
  constructor({ p, pgF, pgB }) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height) : pgB;

    this.bangT = 0;
    this.bangDur = 0.75;
    this.bangCycle = 0;
    this.bangParam = 0;
    this.bangTween = 0;
  }

  bang({ t }) {
    this.bangT = t;
    this.bangParam = Math.floor(Math.random() * 4);
    this.bangCycle = (this.bangCycle + 1) % 2;
  }

  update({ t }) {
    let p = this.p;
    this.bangTween = p.constrain((t - this.bangT) / this.bangDur, 0, 1);
  }
}

class CutGraphics extends LayerGraphics {
  constructor({ p, pgF, pgB, fore, back }) {
    super({ p, pgF, pgB });
    this.foreDraw = fore;
    this.backDraw = back;
  }

  bang({ t, next }) {
    this.foreDraw = this.backDraw;
    this.backDraw = next;
    super.bang({ t });
  }

  update({ t }) {
    let p = this.p;
    super.update({ t });

    this.backDraw.draw({ pg: this.pgB });
    this.foreDraw.draw({ pg: this.pgF });
  }

  draw({ pg }) {
    let p = this.p;

    pg.image(this.pgB, 0, 0);
    pg.push();
    pg.imageMode(p.CENTER);
    pg.translate(width / 2, height / 2);
    pg.rotate(this.bangParam * Math.PI / 2);
    pg.translate(-EasingFunctions.easeInOutCubic(this.bangTween) * width, 0);
    pg.rotate(-this.bangParam * Math.PI / 2);
    pg.image(this.pgF, 0, 0);
    pg.pop();
  }
}

class WipeDelayGraphics extends LayerGraphics {
  constructor({ p, pgF, pgB, fore, back, wipe }) {
    super({ p, pgF, pgB });
    this.pgMask = p.createGraphics(width, height);
    this.pgbF = p.createGraphics(width, height);
    this.pgbB = p.createGraphics(width, height);
    this.foreDraw = fore;
    this.backDraw = back;
    this.wipeDraw = wipe;
    this.seq = 0;
    this.bangDur = 1;
  }

  bang({ t, next, wipe }) {
    this.seq = (this.seq + 1) % 2;
    if (this.seq == 0) {
      this.foreDraw = this.backDraw;
      this.backDraw = next;
      this.wipeDraw = wipe;
      super.bang({ t });
    }
    else {
      this.bangT = t;
    }
  }

  update({ t }) {
    let p = this.p;
    super.update({ t });

    setColorMode = 0;
    this.backDraw.draw({ pg: this.pgB });
    this.foreDraw.draw({ pg: this.pgF });
    setColorMode = 2;
    let tw = 0;
    if (this.seq == 0) {
      tw = this.bangTween * 0.5;
    }
    else {
      tw = this.bangTween * 0.5 + 0.5;
    }
    let args = { tw, bangParam: this.bangParam };

    this.wipeDraw.draw({ pg: this.pgMask, args });
    setColorMode = 0;

    this.pgbB.blendMode(p.BLEND);
    this.pgbB.background(0);
    this.pgbB.image(this.pgB, 0, 0);
    this.pgbB.blendMode(p.MULTIPLY);
    this.pgbB.image(this.pgMask, 0, 0);

    this.pgMask.filter(p.INVERT);

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

class StripeWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam + 1;
    let r = pg.width / 1.4 / n;
    pg.rectMode(p.CENTER);
    for (let i = -n; i <= n; i++) {
      pg.rect(i * r, 0, p.lerp(0, r, EasingFunctions.easeInOutCubic(tw)), pg.height * 2);
    }
  }
}
class CircleExpandWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam;
    let r = p.lerp(0, pg.width * 1.42, EasingFunctions.easeInOutCubic(tw)) / (n * 2 + 1) * 2;
    for (let i = -n; i <= n; i++) {
      for (let j = -n; j <= n; j++) {
        pg.ellipse(pg.width / 2 / n * j, pg.width / 2 / n * i, r);
      }
    }
  }
}
class ShapeExpandWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam + 3;
    let r = p.lerp(0, pg.width * 1.42, EasingFunctions.easeInOutCubic(tw));
    pg.beginShape();
    for (let i = 0; i <= n; i++) {
      let theta = i / n * Math.PI * 2 - Math.PI / 2;
      let x = r * Math.cos(theta);
      let y = r * Math.sin(theta);
      pg.vertex(x, y);
    }
    pg.endShape(p.CLOSE);
  }
}
class ClockWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
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
  }
}
class HorizontalBoxPushWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(bangParam / 2 * Math.PI);
    pg.translate(-pg.width / 2, -pg.height / 2);
    let r = p.lerp(0, pg.width, EasingFunctions.easeInOutCubic(tw));
    pg.rect(0, 0, r, pg.height);
  }
}

class WotC {
  constructor({ p }) {
    this.p = p;
    this.circleCnt = 0;
    this.vertexCnt = 0;
    this.MAX_CIRCLE_CNT = 200;
    this.MIN_CIRCLE_CNT = 50;
    this.MAX_VERTEX_CNT = 30;
    this.MIN_VERTEX_CNT = 3;
  }

  draw(pg, args) {
    let { col, sides } = args;
    setColor(pg, 'background', col.bg);
    // Waltz of the Circles by MiniPear
    // https://www.openprocessing.org/sketch/748916
    let { r0, r1 } = args;
    let p = this.p;
    pg.push();
    pg.translate(pg.width / 2, pg.height / 2);

    this.circleCnt = p.int(p.map(r0, 0, 1, this.MAX_CIRCLE_CNT, this.MIN_CIRCLE_CNT));
    this.vertexCnt = [3, 4, 5, 6, 100][Math.floor(r1 * 5)];
    // this.vertexCnt = p.int(p.map(r1, 0, 1, this.MAX_VERTEX_CNT, this.MIN_VERTEX_CNT));

    let idx = 0, index = col.fg;
    let c0 = p.color(colorSchemes[idx].get(index).r, colorSchemes[idx].get(index).g, colorSchemes[idx].get(index).b);
    index = (col.fg + 2) % 5;
    let c1 = p.color(colorSchemes[idx].get(index).r, colorSchemes[idx].get(index).g, colorSchemes[idx].get(index).b);

    for (let ci = 0; ci < this.circleCnt; ci++) {
      let time = p.frameCount / 20;
      let thetaC = p.map(ci, 0, this.circleCnt, 0, p.TAU);
      let scale = pg.width / 7 * 3;

      let circleCenter = this.getCenterByTheta(thetaC, time, scale);
      let circleSize = this.getSizeByTheta(thetaC, time, scale);
      // let c = this.getColorByTheta(thetaC, time);

      // pg.stroke(c);
      // setColor(pg, 'stroke', col.fg);
      let th = 8.0 * thetaC + time * 2.0;
      let c = p.lerpColor(c0, c1, Math.cos(th) * 0.5 + 0.5);
      pg.stroke(c);
      pg.noFill();
      if (this.vertexCnt > 10) {
        pg.ellipse(circleCenter.x, circleCenter.y, circleSize * 2);
      } else {
        pg.beginShape();
        for (let vi = 0; vi < this.vertexCnt; vi++) {
          let thetaV = p.map(vi, 0, this.vertexCnt, 0, p.TAU);
          let x = circleCenter.x + Math.cos(thetaV) * circleSize;
          let y = circleCenter.y + Math.sin(thetaV) * circleSize;
          pg.vertex(x, y);
        }
        pg.endShape(p.CLOSE);
      }
    }
    pg.pop();
  }

  getCenterByTheta(theta, time, scale) {
    let p = this.p;
    let direction = p.createVector(Math.cos(theta), Math.sin(theta));
    let distance = 0.6 + 0.2 * Math.cos(theta * 6.0 + Math.cos(theta * 8.0 + time));
    let circleCenter = direction.mult(distance * scale);
    return circleCenter;
  }

  getSizeByTheta(theta, time, scale) {
    let offset = 0.2 + 0.12 * Math.cos(theta * 9.0 - time * 2.0);
    let circleSize = scale * offset;
    return circleSize;
  }

  getColorByTheta(theta, time) {
    let p = this.p;
    let th = 8.0 * theta + time * 2.0;
    let r = 0.6 + 0.4 * Math.cos(th),
      g = 0.6 + 0.4 * Math.cos(th - Math.PI / 3),
      b = 0.6 + 0.4 * Math.cos(th - Math.PI * 2.0 / 3.0),
      alpha = p.map(this.circleCnt, this.MIN_CIRCLE_CNT, this.MAX_CIRCLE_CNT, 150, 100);
    return p.color(r * 255, g * 255, b * 255, alpha);
  }
}

class PF {
  constructor({ p }) {
    this.p = p;
    this.rot = 0;
    this.freq = 0.000005;
    this.cont = 0;
  }
  draw(pg, args) {
    let { col, sides } = args;
    setColor(pg, 'background', col.bg);
    // particleFlow by yasai
    // https://www.openprocessing.org/sketch/422446
    let p = this.p;
    pg.translate(width / 2, height / 2);
    pg.rotate(p.radians(this.rot));

    pg.ellipseMode(p.RADIUS);
    for (let i = 0; i < 200; i++) {
      let circle = width / 3 + width / 6 * 0.5 * Math.sin(p.millis() * this.freq * i);
      let alpha = p.map(circle, 150, 250, 255, 60);
      let r = p.map(circle, 150 / 600 * width, 250 / 600 * width, 5, 2) / 600 * width;
      setColor(pg, 'fill', col.fg, alpha);
      pg.noStroke();
      pg.ellipse(circle * Math.cos(i), circle * Math.sin(i), r, r);
      this.rot = this.rot + 0.00005 * 4;
    }
  }
}

class Blobs {
  constructor({ p }) {
    this.p = p;
    this.handle_len_rate = 3;
    this.maxDistance = 200;
    this.circlePaths = [];
    this.connections = [];
    this.numBlobs = 8;

    this.setup();
  }

  setup() {
    const p = this.p;
    this.radius = 120 * 8;
    //generate circles
    for (let i = 0; i < this.numBlobs; i++) {
      this.circlePaths.push({
        position: p.createVector(p.random(width), p.random(height)),
        radius: this.radius,//i == 0 ? 120 : p.random(100, 120),
        vel: p5.Vector.random2D().mult(2)
      });
    }
    this.circlePaths[0].radius = 250 * 0.4;
  }

  draw(pg, args) {
    const { col, sides } = args;
    const { tw, bangParam } = args;
    pg.push();
    if (tw == undefined) {
      setColor(pg, 'background', col.bg);
      setColor(pg, 'fill', col.fg);
    }
    else {
      pg.background(0);
      pg.fill(255)
    }
    // My Sketch by Mara@website.com
    // https://www.openprocessing.org/sketch/757327
    const p = this.p;
    let radius = this.radius;
    if (tw < 0.5) {
      radius *= p.map(EasingFunctions.easeInOutCubic(tw * 2), 0, 1, 0, 0.125);
    }
    else {
      radius *= p.map(EasingFunctions.easeInOutCubic(tw * 2 - 1), 0, 1, 0.125, 1);
    }
    if (tw == undefined)
      radius = this.radius / 8;
    pg.noStroke();
    this.circlePaths.forEach((circle, index) => {
      circle.radius = radius;
    });

    //draw circles
    this.circlePaths.forEach((circle, index) => {
      let position = circle.position;
      position.add(circle.vel);
      let d = width / 10;
      if (position.x > width + d) position.x = position.x - (width + d);
      else if (position.x < -d) position.x = width + d - position.x;
      if (position.y > height + d) position.y = position.y - (height + d);
      else if (position.y < -d) position.y = height + d - position.y;
      pg.ellipse(position.x, position.y, circle.radius, circle.radius)
    })

    //generate connections
    this.connections.length = 0;
    for (let i = 0, l = this.circlePaths.length; i < l; i++) {
      for (let j = i - 1; j >= 0; j--) {
        let path = this.metaball(this.circlePaths[i], this.circlePaths[j], 0.5, this.handle_len_rate, this.maxDistance);
        if (path) {
          this.connections.push(path);
        }
      }
    }

    //draw connections
    this.connections.forEach(path => {
      pg.beginShape();
      for (let j = 0; j < 4; j++) {
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
    pg.pop();
  }

  metaball(ball1, ball2, v, handle_len_rate, maxDistance) {
    let p = this.p;
    let radius1 = ball1.radius / 2;
    let radius2 = ball2.radius / 2;
    let center1 = ball1.position;
    let center2 = ball2.position;
    let d = center1.dist(center2);
    let u1 = 0;
    let u2 = 0;
    if (d > maxDistance || d <= Math.abs(radius1 - radius2)) {
      return;
    } else if (d < radius1 + radius2) {
      // case circles are overlapping
      u1 = Math.acos((radius1 * radius1 + d * d - radius2 * radius2) / (2 * radius1 * d));
      u2 = Math.acos((radius2 * radius2 + d * d - radius1 * radius1) / (2 * radius2 * d));
    }
    let angle1 = Math.atan2(center2.y - center1.y, center2.x - center1.x);
    let angle2 = Math.acos((radius1 - radius2) / d);
    let angle1a = angle1 + u1 + (angle2 - u1) * v;
    let angle1b = angle1 - u1 - (angle2 - u1) * v;
    let angle2a = angle1 + Math.PI - u2 - (Math.PI - u2 - angle2) * v;
    let angle2b = angle1 - Math.PI + u2 + (Math.PI - u2 - angle2) * v;
    let p1a = p5.Vector.add(center1, p5.Vector.fromAngle(angle1a, radius1));
    let p1b = p5.Vector.add(center1, p5.Vector.fromAngle(angle1b, radius1));
    let p2a = p5.Vector.add(center2, p5.Vector.fromAngle(angle2a, radius2));
    let p2b = p5.Vector.add(center2, p5.Vector.fromAngle(angle2b, radius2));
    // define handle length by the distance between
    // both ends of the curve to draw
    let d2 = Math.min(v * handle_len_rate, p.dist(p1a.x, p1a.y, p2a.x, p2a.y) / (radius1 + radius2));
    // case circles are overlapping:
    d2 *= Math.min(1, d * 2 / (radius1 + radius2));
    radius1 *= d2;
    radius2 *= d2;
    let path = {
      segments: [p1a, p2a, p2b, p1b],
      handles: [
        p5.Vector.fromAngle(angle1a - Math.PI / 2, radius1),
        p5.Vector.fromAngle(angle2a + Math.PI / 2, radius2),
        p5.Vector.fromAngle(angle2b - Math.PI / 2, radius2),
        p5.Vector.fromAngle(angle1b + Math.PI / 2, radius1)
      ]
    };
    return path;
  }
}

class CircleGrid {
  draw(pg, args) {
    let { col, sides } = args;
    setColor(pg, 'background', col.bg);
    setColor(pg, 'fill', col.fg);
    pg.noStroke();
    let n = sides + 1;
    let r = pg.width / n / 4;
    pg.translate(pg.width / 2, pg.height / 2);
    for (let i = -n; i <= n; i++) {
      for (let j = -n; j <= n; j++) {
        pg.push();
        pg.translate(pg.width / 2 / n * j, pg.width / 2 / n * i);
        pg.ellipse(0, 0, r, r);
        pg.pop();
      }
    }
  }
}

class SquareGrid {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
    let { col, sides } = args;
    let n = sides + 1;
    let r = pg.width / n / 4 * Math.sqrt(2);

    let t = p.millis() * 0.001;
    if ((t + 1) % 4 < 2) {
      setColor(pg, 'background', col.bg);
      setColor(pg, 'fill', col.fg);
    }
    else {
      setColor(pg, 'background', col.fg);
      setColor(pg, 'fill', col.bg);
      pg.translate(pg.width / 2 / n * 0.5, pg.width / 2 / n * 0.5);
    }
    pg.noStroke();
    pg.rectMode(p.CENTER);
    pg.translate(pg.width / 2, pg.height / 2);
    for (let i = -n; i <= n; i++) {
      for (let j = -n; j <= n; j++) {
        pg.push();
        pg.translate(pg.width / 2 / n * j, pg.width / 2 / n * i);
        pg.rotate((EasingFunctions.easeInOutQuint(t % 1) + Math.floor(t)) / 4 * Math.PI);
        pg.rect(0, 0, r, r);
        pg.pop();
      }
    }
  }
}

class NoiseRing {
  constructor({ p }) {
    this.p = p;
    this.n = 12;
    this.phases = new Array(this.n).fill(0);
  }
  draw(pg, args) {
    let p = this.p
    let { col, freq } = args;
    let t = p.millis() * 0.001;
    this.phase += freq * 0.03;
    pg.push();
    setColor(pg, 'fill', col.bg, 100);
    pg.rectMode(pg.CORNER);
    pg.noStroke();
    pg.rect(0, 0, pg.width, pg.height);

    pg.noFill();
    pg.strokeWeight(pg.width / 100);
    pg.translate(pg.width / 2, pg.height / 2);
    let W = pg.width * 0.1;
    let w = pg.width / 30;
    let n = this.n;
    for (let i = 0; i < n; i++) {
      pg.push();
      let scolor = i % 4;
      if (scolor == col.bg) {
        scolor = (scolor + 1) % 4;
      }
      setColor(pg, 'stroke', scolor);
      let note = p.map(freq, 20, 60, 0, n);
      let dist = p.map(Math.abs(i - note), 0, 4, 1, 0);
      let noiseFactor = 0;
      if (dist > 0) noiseFactor = dist;
      this.phases[i] += noiseFactor;
      pg.rotate((this.phases[i] + t * 0.5) * (n - i) / n);
      pg.beginShape();
      let N = 128;
      for (let j = 0; j < N; j++) {
        let r = (i + noiseFactor * Math.random()) * w + W;
        let theta = j / N * Math.PI;
        let x = r * Math.cos(theta);
        let y = r * Math.sin(theta);
        pg.vertex(x, y);
      }
      pg.endShape();
      // pg.arc(0, 0, i * w + W, i * w + W, 0, Math.PI * 0.5*(freq+1)/30);
      // pg.arc(0, 0, i * w + W, i * w + W, Math.PI, Math.PI * 1.5*(freq+1)/30);
      pg.pop();
    }
    pg.pop();
  }
}

Array.prototype.repeat = function (n) {
  let ret = [];
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < this.length; j++) {
      ret.push(this[j]);
    }
  }
  return ret;
}

Array.prototype.synth = function (synth) {
  return this.map(i => i + synth);
}

const s = (p) => {
  const wipeDraws = [
    new Blobs({ p }),
    // new StripeWipe({ p }),
    // new CircleExpandWipe({ p }),
    // new ShapeExpandWipe({ p }),
    // new ClockWipe({ p }),
    // new HorizontalBoxPushWipe({ p }),
  ];
  const solidDraws = [
    // new WotC({ p }),
    // new PF({ p }),
    // new Blobs({ p }),
    // new Blobs({ p }),
    new Blobs({ p }),
    new SquareGrid({ p }),
    new CircleGrid({ p }),
  ];
  const foreDrawer = new Drawer({ c: new NoiseRing({ p }), args: { col: { fg: 1, bg: 4 } } });
  const synths = {};

  let freq = 0;
  let pointer = 0;
  let codeInput;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = `[30,30,40,40].synth(p).concat([20,20,30,30].synth(f)).repeat(4).concat('70~').repeat(4)`;
  let pastCommands = [];

  let wipe0, cut0;
  let glitch0;
  let turn;

  p.setup = () => {
    p.createCanvas(width, height);
    p.frameRate(30);

    wipe0 = new WipeDelayGraphics({
      p,
      fore: new Drawer({ c: solidDraws[1] }),
      back: new Drawer({ c: solidDraws[0] }),
      wipe: new Drawer({ c: wipeDraws[0], args: { wipe: wipe0 } })
    });

    cut0 = new CutGraphics({
      p,
      fore: new Drawer({ c: solidDraws[1] }),
      back: new Drawer({ c: solidDraws[0] }),
    });

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

    codeInput = p.createInput(codeBase);
    codeInput.size(p.width * 1.5);

    // codeInput.elt.onkeyup = runButtonClicked;
  }

  p.mouseClicked = () => {
    runButtonClicked();
  }

  let node;
  let lastNode;
  let curSynth;
  let curPattern = 0;
  let curDraw = 0;

  p.draw = () => {
    let t = p.millis() * 0.001;

    if (isPlaying) {
      if (pointer < tokens.length) {
        lastNode = node;
        node = tokens[pointer];
        execute(node);
        if (node == '~') {
          curPattern = (curPattern + 1) % wipeDraws.length;
          curDraw = (curDraw + 1) % solidDraws.length;
          // turn = p.random([wipe0, wipe0, wipe0, cut0]);
          turn = wipe0;
          next = new Drawer({ c: solidDraws[curDraw] })
          // next = new Drawer({ c: p.random(solidDraws) })
          wipe0.bang({
            t, next,
            wipe: new Drawer({
              c: wipeDraws[curPattern],
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

    turn.update({ t });
    if (false && (curSynth == 'n' || curSynth == 'm')) {
      turn.draw({ pg: glitch0.pgF });
      glitch0.draw({ pg: p });
    }
    else {
      turn.draw({ pg: p });
    }

    foreDrawer.draw({ pg: p, args: { freq: freq * isPlaying } });
  }

  const runButtonClicked = () => {
    isPlaying = true;

    let code = codeInput.value();
    code = unpack(code);

    let lex = code.match(/(\D+)|[+-]?(\d*[.])?\d+/gi);
    parse(lex);
  }

  const unpack = (code) => {
    let result = '';
    function looseJsonParse(obj) {
      return Function(`"use strict";
      let a='a';let f='f';let N='N';let p='p';let m='m';let s='~';let t='^';let q='[';let n='n'
      return (` + obj + ')')();
    }
    let c = looseJsonParse(code);
    for (let i = 0; i < c.length; i++) {
      result += c[i];
    }
    return result;
  }


  const parse = (l) => {
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

  const execute = (t) => {
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