---
layout: post
title:  "Sound Texture Study 29"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2020-02-10-sound-texture-study-29.png
description: "A sketch"
featured: true
comments: true
p5: true
tonejs: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start. This program uses `ScriptProcessorNode`.

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

const replayMode = true;

const vert = `
#ifdef GL_ES
precision highp float;
precision highp int;
#endif
// attributes, in
attribute vec3 aPosition;
attribute vec3 aNormal;
attribute vec2 aTexCoord;
attribute vec4 aVertexColor;

// attributes, out
varying vec3 var_vertPos;
varying vec4 var_vertCol;
varying vec3 var_vertNormal;
varying vec2 var_vertTexCoord;

// matrices
uniform mat4 uModelViewMatrix;
uniform mat4 uProjectionMatrix;
uniform mat3 uNormalMatrix;

void main() {
  gl_Position = uProjectionMatrix * uModelViewMatrix * vec4(aPosition, 1.0);

  // just passing things through
  var_vertPos      = aPosition;
  var_vertCol      = aVertexColor;
  var_vertNormal   = aNormal;
  var_vertTexCoord = aTexCoord;
}
`;

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
        let a = undefined;
        if (cs[i].length == 8) {
          a = parseInt("0x" + cs[i].substring(6, 8));
        }
        this.colors.push({
          r, g, b, a
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
  new ColorScheme("https://coolors.co/eccbd9-e1eff6-97d2fb-83bcff-80ffe8"),
  new ColorScheme("https://coolors.co/80ffe8-eccbd9-e1eff6-97d2fb-83bcff"),
  new ColorScheme("https://coolors.co/ff0000-00ff00-0000ff-00000000-aaaaaa"),
  new ColorScheme("https://coolors.co/ffffff-808080-000000-333333-aaaaaa"),
];

function setColor(parent, func, index, alpha) {
  let idx = setColorMode;
  let a = colorSchemes[idx].get(index).a;
  if (a == undefined) a = 255;
  if (alpha != undefined) a *= alpha;
  parent[func](colorSchemes[idx].get(index).r, colorSchemes[idx].get(index).g, colorSchemes[idx].get(index).b, a);
}

// https://gist.github.com/gre/1650294/
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t * t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t * (2 - t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t * t * t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t) * t * t + 1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t * t * t * t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1 - (--t) * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t * t * t * t * t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1 + (--t) * t * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuint: function (t) { return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t },
  // easeInElastic: function (t) { return (.04 - .04 / t) * Math.sin(25 * t) + 1 },
  // easeOutElastic: function (t) { return .04 * t / (--t) * Math.sin(25 * t) },
  // easeInOutElastic: function (t) { return Math.pow(2, -10*t) * Math.sin((t - .075)*(2+Math.PI)/.3)+1 },
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
      args.sides = Math.floor(Math.random() * 3);
      args.rand = [];
      for (let i = 0; i < 8; i++) {
        args.rand.push(Math.random());
      }
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
    this.pgF = pgF == undefined ? p.createGraphics(width, height, p.WEBGL) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height, p.WEBGL) : pgB;

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
  constructor({ p, pgF, pgB, fore, back, wipe, switcherCallback }) {
    const frag = `
#ifdef GL_ES
precision highp float;
precision highp int;
#endif

uniform float time;
uniform vec4 col;
//uniform vec2 center;   
uniform vec2 resolution;
uniform sampler2D ppixelsR;
uniform sampler2D ppixelsG;
uniform sampler2D ppixelsB;
uniform sampler2D ppixelsM;
varying vec2 vTexCoord;

void main( void ) {
  vec2 uv = gl_FragCoord.xy / resolution.xy;//vTexCoord;
  uv.y = 1.0 - uv.y;

  vec4 colR = texture2D(ppixelsR, uv);
  vec4 colG = texture2D(ppixelsG, uv);
  vec4 colB = texture2D(ppixelsB, uv);
  vec4 colM = texture2D(ppixelsM, uv);

  gl_FragColor = colR * colM.r + colG * colM.g + colB * colM.b;

}
`;
    super({ p, pgF, pgB });
    this.pgMask = p.createGraphics(width, height, p.WEBGL);
    this.pgM = p.createGraphics(width, height, p.WEBGL);
    this.foreDraw = fore;
    this.backDraw = back;
    this.wipeDraw = wipe;
    this.next = undefined;
    this.nextWipe = undefined;
    this.bangDur = 1.5;
    this.switcherCallback = switcherCallback;
    this.pShader = new p5.Shader(p._renderer, vert, frag);
    // this.lastUpdateFrame = -1;
  }

  bang({ t, next, wipe }) {
    this.next = next;
    this.nextWipe = wipe;
    // super.bang({ t });
    this.bangT = t;
  }

  update({ t }) {
    let p = this.p;
    // if (p.frameCount <= this.lastUpdateFrame) {
    //   return;
    // }
    // this.lastUpdateFrame = p.frameCount;
    super.update({ t });

    if (this.next != undefined && this.bangTween >= 0.5) {
      this.bangParam = Math.floor(Math.random() * 4);
      this.bangCycle = (this.bangCycle + 1) % 2;
      if (this.wipeDraw.c.isWipe) {
        this.foreDraw = this.backDraw;
        this.backDraw = this.next;
      }
      this.next = undefined;

      this.wipeDraw = this.nextWipe;
      this.nextWipe = undefined;
      this.switcherCallback();
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
    this.pgMask.push();
    this.wipeDraw.draw({ pg: this.pgMask, args });
    this.pgMask.pop();
    setColorMode = 0;
  }

  draw({ pg }) {
    const p = this.p;

    this.pShader.setUniform("ppixelsR", this.pgF);
    this.pShader.setUniform("ppixelsG", this.pgM);
    this.pShader.setUniform("ppixelsB", this.pgB);
    this.pShader.setUniform("ppixelsM", this.pgMask);
    this.pShader.setUniform("resolution", [width * p.pixelDensity(), height * p.pixelDensity()]);
    pg.push();
    pg.shader(this.pShader);
    pg.noStroke();
    pg.fill(255);
    pg.rect(-width / 2, -height / 2, width, height);
    pg.resetShader();
    pg.pop();
  }
}

class ClockWipe {
  constructor({ p }) {
    this.p = p;
    this.isWipe = true;
    this.name = 'ClockWipe';
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides, rand, tw, bangParam } = args;
    pg.push();
    setColor(pg, 'background', col.bg);
    // pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    let rate0 = 0;
    let rate1 = 0;
    let r1 = pg.width / 2;
    let rMax = pg.width * 2;
    let rr = 0;
    // if (rand[4] < 0.5) {
    //   r1 = pg.width * 2;
    // }
    let halfRate = col.mg == undefined ? 1 / 2 : 1 / 3;
    const halfRateDiv = Math.floor(rand[0] * 5 + 1);
    halfRate /= halfRateDiv;
    if (rand[3] < 0.5) {
      this.isWipe = true;
    }
    else {
      this.isWipe = false;
    }
    if (tw <= 0.5) {
      const etw = EasingFunctions.easeInOutCubic(tw * 2);
      rate0 = etw * halfRate * 2;
      rate1 = etw * halfRate;
    }
    else {
      const etw = EasingFunctions.easeInOutCubic(tw * 2 - 1);
      if (this.isWipe) {
        rate0 = p.map(etw, 0, 1, 2 * halfRate, 1);
        rate1 = p.map(etw, 0, 1, 1 * halfRate, 1);
        r1 = p.map(etw, 0, 1, r1, rMax);
      }
      else {
        if (rand[5] < 0.5) {
          rate0 = p.map(etw, 0, 1, 2 * halfRate, 0);
          rate1 = p.map(etw, 0, 1, 1 * halfRate, 0);
        }
        else {
          rate0 = 2 * halfRate;
          rate1 = 1 * halfRate;
          rr = p.map(etw, 0, 1, 0, 1);
        }
      }
    }
    function drawArc(r0, r1, rate) {
      const n = 64;
      const sign = bangParam % 2 == 0 ? -1 : 1;
      pg.beginShape(p.TRIANGLE_STRIP);
      for (let i = 0; i <= n; i++) {
        let theta = sign * i / n * Math.PI * 2 * rate - Math.PI / 2;
        let x = r0 * Math.cos(theta);
        let y = r0 * Math.sin(theta);
        pg.vertex(x, y);
        x = r1 * Math.cos(theta);
        y = r1 * Math.sin(theta);
        pg.vertex(x, y);
      }
      pg.endShape();
    }
    const N = Math.floor(4 * rand[1]);
    const M = Math.floor(rand[6] * halfRateDiv + 1);
    for (let i = 0; i <= N; i++) {
      const R1 = p.map(i, 0, N + 1, 0, r1);
      const R0 = p.lerp(p.map(i - 1, 0, N + 1, 0, r1), R1, rr);
      for (let j = 0; j < M; j++) {
        pg.push();
        pg.rotate((i + j) * halfRate * Math.PI * 4 - ((i * 0.25 + 1) * p.millis() * 0.001 + rand[2]) * Math.PI * 0.25);
        if (col.mg != undefined) {
          setColor(pg, 'fill', col.mg);
          drawArc(R0, R1, rate0);
        }
        pg.rotate(halfRate * Math.PI * 4);
        setColor(pg, 'fill', col.fg);
        drawArc(R0, R1, rate1);
        pg.pop();
      }
    }
    pg.pop();
  }
}

class Cube {
  constructor({ p }) {
    this.p = p;
    this.isWipe = false;
    this.name = 'Cube';
  }
  doWipe(rand) {
    return rand[0] < 0.5;
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides, rand, tw, bangParam } = args;
    const t = p.millis() * 0.001;
    let tween;
    this.isWipe = this.doWipe(rand);
    if (tw <= 0.5) {
      tween = EasingFunctions.easeInOutCubic(tw * 2);
    }
    else {
      if (this.doWipe(rand)) {
        tween = EasingFunctions.easeInOutCubic(p.map(tw, 0.5, 1, 1, 3));
      }
      else {
        tween = EasingFunctions.easeInOutCubic(p.map(tw, 0.5, 1, 1, 0));
      }
    }

    const draw = (R, tween) => {
      pg.push();
      let scz = 1;
      if (tw > 0.5 && this.doWipe(rand)) {
        scz = EasingFunctions.easeInOutCubic(p.map(tw, 0.5, 1, 1, 0.1));
      }
      pg.scale(1, 1, scz);

      pg.rotateX(t);
      pg.rotateY(t * 0.5);
      pg.box(R);
      pg.pop();
    }

    pg.push();
    let fading = 1;
    if (tw > 0.5) {
      fading = p.map(tw, 0.5, 1, 1, 0);
    }
    let mode = 'stroke';
    // let mode = 'fill';
    if (mode == 'stroke') {
      pg.noFill();
      pg.strokeWeight(width * 0.05 * fading);
    }
    else {
      pg.noStroke();
    }
    setColor(pg, 'background', col.bg);
    if (col.mg != undefined) {
      setColor(pg, mode, col.mg);
      // draw(pg.width * 0.5 * 1.05 * tween);
    }
    else {
      pg.noStroke();
    }
    if (this.doWipe(rand) == false && rand[1] < 10.5) {
      pg.normalMaterial();
    }
    else {
      setColor(pg, 'fill', col.fg);
    }
    draw(pg.width * 0.5 * tween);
    pg.pop();
  }
}

// reference https://yoppa.org/proga10/1271.html
class Particle {
  constructor({ p, x, y, r }) {
    this.p = p;
    this.x = x;
    this.y = y;
    this.org = { x, y };
    this.v = { x: 0, y: 0 };
    this.r = r;
    this.last = { x, y };
    this.springLength = width * 0.02;
    this.stiffness = 0.2;
    this.damping = 0.7;
    this.mass = 1;
  }
  springForce(a, neighbor) {
    const p = this.p;
    const l = p.dist(this.last.x, this.last.y, neighbor.last.x, neighbor.last.y);
    const f = this.stiffness * (this.springLength - l);
    const A = f / this.mass;
    a.x += A * (this.x - neighbor.x) / l;
    a.y += A * (this.y - neighbor.y) / l;
  }

  preDraw({ distX, distY }) {
    const p = this.p;
    const a = { x: 0, y: 0 };
    if (this.next)
      this.springForce(a, this.next);
    if (this.prev)
      this.springForce(a, this.prev);
    {
      const l = 1 + p.dist(this.last.x, this.last.y, distX, distY);
      const f = 100 / l;
      const A = f / this.mass;
      a.x += A * (this.x - distX) / l;
      a.y += A * (this.y - distY) / l;
    }
    {
      const l = 1 + p.dist(this.last.x, this.last.y, this.org.x, this.org.y);
      const f = -l * 0.02;
      const A = f / this.mass;
      a.x += A * (this.x - this.org.x) / l;
      a.y += A * (this.y - this.org.y) / l;
    }
    this.v.x = this.damping * this.v.x + a.x;
    this.v.y = this.damping * this.v.y + a.y;
    this.x += this.v.x;
    this.y += this.v.y;
    if (this.x < 0) this.x = 0;
    if (this.y < 0) this.y = 0;
    if (this.x > width) this.x = width;
    if (this.y > height) this.y = height;
  }
  draw() {
  }
  postDraw() {
    this.last = { x: this.x, y: this.y };
  }
}

const particles = [];

class Chain {
  constructor({ p }) {
    this.p = p;
    this.particles = [];
    this.isWipe = true;
    const N = 100;
    for (let i = 0; i < N; i++) {
      const radius = width * 0.3;
      const theta = i / N * 2 * Math.PI;
      const x = radius * Math.cos(theta) + width / 2;
      const y = radius * Math.sin(theta) + height / 2;
      const r = width * 0.02;
      this.particles.push(new Particle({ p, x, y, r }));
    }
    for (let i = 0; i < N; i++) {
      if (i < N - 1)
        this.particles[i].next = this.particles[(i + 1) % N];
      if (i > 0)
        this.particles[i].prev = this.particles[(i - 1 + N) % N];
    }
  }

  draw(pg, args) {
    const p = this.p
    const { col, sides, rand, tw, bangParam } = args;
    const t = p.millis() * 0.001;
    const particles = this.particles;
    let tween = 0;
    if (tw <= 0.5) {
      tween = EasingFunctions.easeOutCubic(tw * 2);
    }
    else {
      tween = EasingFunctions.easeInCubic(p.map(tw, 0.5, 1, 0, 1));
    }

    pg.translate(-width / 2, -height / 2);
    setColor(pg, 'background', col.bg);
    pg.noStroke();
    const th = p.noise(t * 2) * Math.PI * 4;
    const distX = width * 0.3 * Math.cos(th) + width / 2;
    const distY = width * 0.3 * Math.sin(th) + height / 2;
    for (const particle of particles) {
      this.springLength = width * p.lerp(0.01, 0.2, rand[0]);
      this.stiffness = p.lerp(0.1, 0.5, rand[1]);
      particle.preDraw({ distX, distY });
    }
    let count = 0;
    for (const particle of particles) {
      if (particle.next == undefined) continue;
      let r = p.dist(particle.last.x, particle.last.y, particle.next.x, particle.next.y);
      if (count != particles.length - 2 && col.mg != undefined) {
        setColor(pg, 'fill', col.mg);
        if (tw <= 0.5) r *= tween;
      }
      else {
        setColor(pg, 'fill', col.fg);
        if (tw <= 0.5) {
          r *= tween;
        }
        else {
          r = p.lerp(r, width * 2, tween);
        }
      }
      pg.ellipse(particle.x, particle.y, r, r);
      if (count != particles.length - 2 && col.mg != undefined) {
        setColor(pg, 'fill', col.fg);
      }
      else {
        setColor(pg, 'fill', col.fg);
      }
      pg.ellipse(particle.x, particle.y, r * 0.8, r * 0.8);
      count++;
    }
    for (const particle of particles) {
      particle.postDraw();
    }
  }
}

class Osc {
  constructor({ p }) {
    this.p = p;
    this.name = 'Osc';
    const frag = `
#ifdef GL_ES
precision highp float;
precision highp int;
#endif

uniform float t;
uniform float freq;
uniform vec4 col0;
uniform vec4 col1;
//uniform vec2 center;   
uniform vec2 resolution;
varying vec2 vTexCoord;

void main( void ) {
  vec2 uv = gl_FragCoord.xy / resolution.xy;
  float c = 0.5 + 0.5 * sin(freq * uv.x * 2.0 * 3.1415 + t);
  gl_FragColor = mix(col0, col1, c);
}
`;
    this.pShader = new p5.Shader(p._renderer, vert, frag);
  }

  draw(pg, args) {
    const p = this.p;
    const { col, sides, rand, tw, bangParam } = args;

    this.pShader.setUniform('t', p.millis() * 0.001);
    this.pShader.setUniform('freq', Math.floor(rand[0] * 8));
    let i0 = col.bg;
    this.pShader.setUniform('col0', [colorSchemes[0].get(i0).r / 255, colorSchemes[0].get(i0).g / 255, colorSchemes[0].get(i0).b / 255, 1]);
    let i1 = col.fg;
    this.pShader.setUniform('col1', [colorSchemes[0].get(i1).r / 255, colorSchemes[0].get(i1).g / 255, colorSchemes[0].get(i1).b / 255, 1]);
    // this.pShader.setUniform('col0', [1, 0, 0, 1]);
    // this.pShader.setUniform('col1', [0, 1, 1, 1]);
    this.pShader.setUniform('resolution', [width * p.pixelDensity(), height * p.pixelDensity()]);
    pg.push();
    pg.shader(this.pShader);
    pg.noStroke();
    pg.fill(255);
    pg.rect(-width / 2, -height / 2, width, height);
    pg.resetShader();
    pg.pop();
  }
}

class CircleGridMove {
  constructor({ p }) {
    this.p = p;
    this.name = 'CircleGridMove';
  }
  draw(pg, args) {
    const p = this.p;
    const { col, sides, rand, tw, bangParam } = args;
    pg.push();
    setColor(pg, 'background', col.bg);
    setColor(pg, 'fill', col.fg);
    pg.noStroke();
    const n = sides + 1;
    const r = pg.width / n / 4;
    const t = p.millis() * 0.001;
    // pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(sides * Math.PI / 4);
    for (let j = -n - 1; j <= n + 1; j++) {
      const sj = rand[0] > 0.5 ? (j + n + 1) / (2 * n + 2) : 0;
      const tww = p.constrain(p.map(t % 1, 0, 1 - sj, 0, 1), 0, 1);
      const dt = EasingFunctions.easeInOutCubic(tww) * pg.width / 2 / n;
      for (let i = -n - 1; i <= n + 1; i++) {
        pg.push();
        const dx = (i + n + 1) % 2 == Math.floor(t) % 2 ? dt : 0;
        pg.translate(pg.width / 2 / n * j + dx, pg.width / 2 / n * i);
        pg.ellipse(0, 0, r, r);
        pg.pop();
      }
    }
    pg.pop();
  }
}

class SquareGridRotate {
  constructor({ p }) {
    this.p = p;
    this.name = 'SquareGridRotate';
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides, rand, tw, bangParam } = args;
    pg.push();
    const n = sides + 1;
    const r = pg.width / n / 4 * Math.sqrt(2);

    const t = p.millis() * 0.001;
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
    // pg.translate(pg.width / 2, pg.height / 2);
    for (let i = -n; i <= n; i++) {
      for (let j = -n; j <= n; j++) {
        pg.push();
        pg.translate(pg.width / 2 / n * j, pg.width / 2 / n * i);
        pg.rotate((EasingFunctions.easeInOutQuint(t % 1) + Math.floor(t)) / 4 * Math.PI);
        pg.rect(0, 0, r, r);
        pg.pop();
      }
    }
    pg.pop();
  }
}

class SquareGrid {
  constructor({ p }) {
    this.p = p;
    this.name = 'SquareGrid';
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides, rand, tw, bangParam } = args;
    pg.push();
    const n = sides + 1;
    const r = pg.width / n / 4 * Math.sqrt(2);

    const t = p.millis() * 0.001;
    setColor(pg, 'background', col.bg);
    setColor(pg, 'fill', col.fg);
    pg.noStroke();
    pg.rectMode(p.CENTER);
    // pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(Math.PI / 2 * sides);
    for (let j = -n - 1; j <= n + 1; j++) {
      const sj = rand[0] > 0.5 ? (j + n + 1) / (2 * n + 2) : 0;
      const tww = p.constrain(p.map(t % 1, 0, 1 - sj, 0, 1), 0, 1);
      const dt = EasingFunctions.easeInOutCubic(tww) * pg.width / 2 / n;
      for (let i = -n - 1; i <= n + 1; i++) {
        pg.push();
        const dx = (i + n + 1) % 2 == Math.floor(t) % 2 ? dt : 0;
        pg.translate(pg.width / 2 / n * j + dx, pg.width / 2 / n * i);
        pg.rotate(Math.PI / 4 * sides);
        pg.rect(0, 0, r, r);
        pg.pop();
      }
    }
    pg.pop();
  }
}

const s = (p) => {
  const wipeDraws = [
    new Chain({ p }),
    // new Cube({ p }),
    // new ClockWipe({ p }),
    // new ShapeExpandWipe({ p }),
    // new ShapeExpandReturn({ p }),
  ];
  let solidDraws = [
    // new Osc({ p }),
    // new Osc({ p }),
    // new Osc({ p }),
    // new Osc({ p }),
    new SquareGrid({ p }),
    new CircleGridMove({ p }),
    new SquareGridRotate({ p }),
  ];
  const synths = {};
  const feedbackLoop = new FeedbackLoop();

  let freq = 0, freqLerped = 0;
  let pointer = 0;
  let codeInput;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = 'n';
  let pastCommands = [];

  let wipe0;
  let turn;

  let diffDrawer;

  const history = [];
  const savedHistory = ["10<<<<[=>>>>", "10<<<<[=f=>>>>", "<10<<[=>>30<<f=>>>", "<10<<<[=>>>30<<<m=>>>>", "<10<<<+1[=>>><<<m=>>>>", "<10<<<+1[=>>><<<m=n=>>>>", "d<10<<<+2[=>>><<<-1a=>>>>"]
  let curHistory = 0;

  let font;
  let points;
  let bounds;
  p.preload = () => {
    font = p.loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');
  }

  p.setup = () => {
    p.createCanvas(width, height, p.WEBGL);
    p.frameRate(60);

    wipe0 = new WipeDelayGraphics({
      p,
      switcherCallback: p.switcherCallback,
      fore: new Drawer({ c: solidDraws[1] }),
      back: new Drawer({ c: solidDraws[0] }),
      wipe: new Drawer({
        c: wipeDraws[0], args: { col: { bg: 0, fg: 2, mg: 1 } }
      })
    });

    turn = wipe0;

    diffDrawer = new Drawer({ c: solidDraws[2] });

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

    if (replayMode == false) {
      codeInput = p.createInput(codeBase);
      codeInput.size(p.width * 1.5);
    }
    setChar('A');
  }

  p.mouseClicked = () => {
    runButtonClicked();
  }

  function setChar(ltr) {
    points = font.textToPoints(ltr, 0, 0, 10, {
      sampleFactor: 15,
      simplifyThreshold: 0
    });
    bounds = font.textBounds(` ${ltr} `, 0, 0, 10);
    const ps = turn.wipeDraw.c.particles;
    for (let i = 0; i < points.length; i++) {
      let I = Math.floor(p.map(i, 0, points.length, 0, ps.length));
      let pt = points[i];
      ps[I].org.x = width / 4 + pt.x * width / bounds.w - bounds.x * width / bounds.w;
      ps[I].org.y = -height / 4 + pt.y * height / 2 / bounds.h - bounds.y * height / bounds.h;
      // if (i >= ps.length - 1) break;
    }
  }
  let node;
  let curPattern = 0;
  let curDraw = 0;

  let curChar = 0;

  p.switcherCallback = () => {
    diffDrawer = new Drawer({
      c: solidDraws[Math.floor(solidDraws.length * Math.random())],
    });
    // curChar = (curChar + 1) % 26;
    // setChar();
  }
  p.draw = () => {
    let t = p.millis() * 0.001;

    if (isPlaying) {
      freqLerped = p.lerp(freqLerped, freq, 0.3);
      if (pointer < tokens.length) {
        lastNode = node;
        node = tokens[pointer];
        execute(node);
        if(/[^=+-]/.test(node)) {
          setChar(node);
        }
      } else {
        isPlaying = false;
        if (replayMode) {
          curPattern = (curPattern + 1) % wipeDraws.length;
          curDraw = (curDraw + 1) % solidDraws.length;
          turn = wipe0;
          next = new Drawer({ c: solidDraws[curDraw] })
          wipe0.bang({
            t, next,
            wipe: new Drawer({
              c: wipeDraws[curPattern],
              args: {
                col: { bg: 0, fg: 2, mg: Math.random() > 0.5 ? 1 : undefined }
              }
            })
          });
          setTimeout(() => {
            curHistory++;
            if (curHistory < savedHistory.length) {
              runButtonClicked();
            }
            else {
              curHistory = 0;
            }
          }, 500);
        }
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

    turn.freq = freq;
    turn.update({ t });

    diffDrawer.draw({ pg: turn.pgM });

    p.background(0);
    turn.draw({ pg: p });
    // p.image(turn.pgMask, -width / 2, -height / 2, width, height);
  }

  let isSetup = false;
  let runButtonClicked = () => {
    isPlaying = true;
    if (isSetup == false) {
      feedbackLoop.setup();

      for (const key in synths) {
        synths[key].connect(feedbackLoop.feedbackDelay);
      }
      isSetup = true;
    }
    let code;
    if (replayMode) {
      code = savedHistory[curHistory];
    }
    else {
      code = codeInput.value();
    }
    let unbalancedBrackets = (code.split("<").length - 1) - (code.split(">").length - 1);
    if (unbalancedBrackets > 0) {
      code += '>'.repeat(unbalancedBrackets);
    }
    if (replayMode == false) {
      if (history.length == 0 || history[history.length - 1] != code) {
        history.push(code);
        console.log(history);
      }
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
          case 'd':
            if (feedbackLoop.feedbackDelay) {
              feedbackLoop.feedbackDelay.feedback.linearRampTo(0.7, 1 / 30);
            }
            break;
          case 'b':
            if (feedbackLoop.feedbackDelay) {
              feedbackLoop.feedbackDelay.feedback.linearRampTo(0, 1 / 30);
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

class FeedbackLoop {
  constructor() {
    this.isSetup = false;
    this.bufferSize = 1024;
    this.effectiveBufferSize = this.bufferSize;
    this.amp = 0.5;
  }
  setup() {
    if (this.isSetup) return;

    this.feedback = Tone.context.createScriptProcessor(this.bufferSize, 1, 1);
    this.feedbackDelay = new Tone.FeedbackDelay(0.1, 0.0).connect(this.feedback);
    this.feedback.onaudioprocess = (e) => {
      let a = e.inputBuffer.getChannelData(0);
      let output = e.outputBuffer.getChannelData(0);
      for (let i = 0; i < this.bufferSize; i++) {
        output[i] = a[i % this.effectiveBufferSize] * this.amp;
      }
    }

    this.feedback.connect(Tone.Master);
    this.isSetup = true;
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>