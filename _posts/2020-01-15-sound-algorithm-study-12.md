---
layout: post
title:  "Sound Algorithm Study 12"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2020-01-15-sound-algorithm-study-12.png
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
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

// credit
// Mara@website.com https://www.openprocessing.org/sketch/757327

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
  new ColorScheme("https://coolors.co/453823-561f37-39a2ae-55dbcb-75e4b3"),
  new ColorScheme("https://coolors.co/75e4b3-453823-561f37-39a2ae-55dbcb"),
  new ColorScheme("https://coolors.co/000000-808080-ffffff-333333-aaaaaa"),
  new ColorScheme("https://coolors.co/ffffff-808080-000000-333333-aaaaaa"),
];

function setColor(parent, func, index, alpha) {
  let idx = setColorMode;
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[idx].get(index).r, colorSchemes[idx].get(index).g, colorSchemes[idx].get(index).b, alpha);
}

// https://gist.github.com/gre/1650294/
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t*t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t*(2-t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t<.5 ? 2*t*t : -1+(4-2*t)*t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t*t*t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t)*t*t+1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t<.5 ? 4*t*t*t : (t-1)*(2*t-2)*(2*t-2)+1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t*t*t*t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1-(--t)*t*t*t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t<.5 ? 8*t*t*t*t : 1-8*(--t)*t*t*t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t*t*t*t*t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1+(--t)*t*t*t*t },
  // acceleration until halfway, then deceleration 
  easeInOutQuint: function (t) { return t<.5 ? 16*t*t*t*t*t : 1+16*(--t)*t*t*t*t }
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

class WipeDelayGraphics extends LayerGraphics {
  constructor({ p, pgF, pgB, fore, back, wipe }) {
    super({ p, pgF, pgB });
    this.pgMask = p.createGraphics(width, height);
    this.pgbF = p.createGraphics(width, height);
    this.pgbB = p.createGraphics(width, height);
    this.foreDraw = fore;
    this.backDraw = back;
    this.wipeDraw = wipe;
    this.next = undefined;
    this.nextWipe = undefined;
    this.bangDur = 1;
  }

  bang({ t, next, wipe }) {
    this.next = next;
    this.nextWipe = wipe;
    // super.bang({ t });
    this.bangT = t;
  }

  update({ t }) {
    let p = this.p;
    super.update({ t });

    if (this.next != undefined && this.bangTween >= 0.5) {
      this.bangParam = Math.floor(Math.random() * 4);
      this.bangCycle = (this.bangCycle + 1) % 2;

      this.foreDraw = this.backDraw;
      this.backDraw = this.next;
      this.next = undefined;

      this.wipeDraw = this.nextWipe;
      this.nextWipe = undefined;
    }

    setColorMode = 0;
    this.backDraw.draw({ pg: this.pgB });
    this.foreDraw.draw({ pg: this.pgF });
    setColorMode = 2;
    let tw = 0;
    if (this.bangTween < 0.5) {
      tw = this.bangTween + 0.5;
    }
    else {
      tw = this.bangTween - 0.5;
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

class ClockWipe {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    let p = this.p
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
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
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

Array.prototype.rep = function (n) {
  let ret = [];
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < this.length; j++) {
      ret.push(this[j]);
    }
  }
  return ret;
}

Array.prototype.syn = function (synth) {
  return this.map(i => i + synth);
}

const s = (p) => {
  const wipeDraws = [
    new Blobs({ p }),
    new ClockWipe({ p }),
  ];
  const solidDraws = [
    // new Blobs({ p }),
    new SquareGrid({ p }),
    new CircleGrid({ p }),
  ];
  const synths = {};

  let freq = 0;
  let pointer = 0;
  let codeInput;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = `(b=[20,30,40,50]).syn(q).concat(b.reverse().syn(q)).rep(2).concat([60].syn('~')).rep(4)`;
  let pastCommands = [];

  let wipe0;
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

    turn = wipe0;

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
          turn = wipe0;
          next = new Drawer({ c: solidDraws[curDraw] })
          wipe0.bang({
            t, next,
            wipe: new Drawer({
              c: wipeDraws[curPattern],
              args: { wipe: wipe0 }
            })
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
    turn.draw({ pg: p });

    p.loadPixels();
    let pixels = p.pixels;
    let d = p.pixelDensity();
    let meml = 68729;
    let note = p.map(freq, 20, 60, 0, width * height * d * d * 4 - meml);
    pixels.copyWithin(note + freq, 1, meml);
    if (curSynth == '~') {
      // pixels.reverse();
    }
    p.updatePixels();
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
      // return Function(`"use strict";
      return Function(`
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
            for (const key in synths) {
              synths[key].triggerRelease();
            }
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