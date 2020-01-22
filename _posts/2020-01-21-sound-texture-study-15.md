---
layout: post
title:  "Sound Texture Study 15"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2020-01-21-sound-texture-study-15.png
description: "A sketch"
featured: true
comments: true
p5: true
tonejs: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start. Move mouse to change feedback delay. This example uses `ScriptProcessorNode`.

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
  easeInOutQuint: function (t) { return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t }
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

class MousePush {
  constructor({ p }) {
    this.p = p;
    this.mouseX = 0;
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
    pg.noStroke();
    pg.rect(0, 0, p.mouseX, height);
    pg.endShape();
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

const s = (p) => {
  const wipeDraws = [
    // new Blobs({ p }),
    // new ClockWipe({ p }),
    new MousePush({ p })
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

  let codeBase = 'n';
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
      oscillator: { type: 'sine' }
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

    feedbackDelay.delayTime.linearRampTo(p.mouseX / p.width, 1 / 60);
  }

  let running = false;
  let bufferSize = 1024;
  let analyser = new Tone.Analyser({
    size: bufferSize,
    type: 'waveform',
    smoothing: 0.0
  }
  );
  let feedbackDelay = new Tone.FeedbackDelay(0.1, 0.5).connect(analyser);
  let tom = new Tone.AMSynth({
    harmonicity: 1,
    detune: 0,
    oscillator: {
      type: 'sine'
    },
    envelope: {
      attack: 0.01,
      decay: 0.01,
      sustain: 1,
      release: 0.5
    },
    modulation: {
      type: 'sine'
    },
    modulationEnvelope: {
      attack: 0.5,
      decay: 0,
      sustain: 1,
      release: 0.5
    }
  }).connect(feedbackDelay);
  tom.oscillator.frequency.value = 1;
  tom.connect(Tone.Master);

  let feedback = Tone.context.createScriptProcessor(bufferSize, 1, 1);
  feedback.onaudioprocess = function (e) {
    let a = analyser.getValue();
    let output = e.outputBuffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) {
      output[i] = a[i] * 0.95;
    }
  }

  feedback.connect(analyser);
  feedback.connect(Tone.Master);
  let runButtonClicked = () => {
    isPlaying = true;

    // tom.triggerAttackRelease(midiToFreq(60), "4n");
    tom.triggerAttack(midiToFreq(60));
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>