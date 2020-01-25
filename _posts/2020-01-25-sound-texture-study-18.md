---
layout: post
title:  "Sound Texture Study 18"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2020-01-25-sound-texture-study-18.png
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
  new ColorScheme("https://coolors.co/eccbd9-e1eff6-97d2fb-83bcff-80ffe8"),
  new ColorScheme("https://coolors.co/80ffe8-eccbd9-e1eff6-97d2fb-83bcff"),
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

      if (this.wipeDraw.c.isWipe) {
        this.foreDraw = this.backDraw;
        this.backDraw = this.next;
      }
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
    this.isWipe = true;
  }
  draw(pg, args) {
    const p = this.p
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
    pg.rotate(-(p.millis() * 0.001 + sides) * Math.PI * 0.25);
    pg.noStroke();
    const rate = EasingFunctions.easeInOutCubic(tw);
    const n = 128;
    const r = pg.width;
    pg.beginShape();
    pg.vertex(0, 0);
    const sign = bangParam % 2 == 0 ? -1 : 1;
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

class ShapeExpandWipe {
  constructor({ p }) {
    this.p = p;
    this.isWipe = true;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(p.millis() * 0.001 * Math.PI * 0.25);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam + 3;
    let r = pg.width * 1.42;
    if (tw < 0.5) {
      r *= p.map(EasingFunctions.easeInOutCubic(tw * 2), 0, 1, 0, 0.25);
    }
    else {
      r *= p.map(EasingFunctions.easeInOutCubic(tw * 2 - 1), 0, 1, 0.25, 1);
    }
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

class ShapeExpandReturn {
  constructor({ p }) {
    this.p = p;
    this.isWipe = false;
  }
  draw(pg, args) {
    let p = this.p
    let { tw, bangParam } = args;
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(p.millis() * 0.001 * Math.PI * 0.25);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    let n = bangParam + 3;
    let r = pg.width * 1.42;
    if (tw < 0.5) {
      r *= p.map(EasingFunctions.easeInOutCubic(tw * 2), 0, 1, 0, 0.25);
    }
    else {
      r *= p.map(EasingFunctions.easeInOutCubic(tw * 2 - 1), 0, 1, 0.25, 0);
    }
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

class CircleGridMove {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    const p = this.p;
    const { col, sides } = args;
    setColor(pg, 'background', col.bg);
    setColor(pg, 'fill', col.fg);
    pg.noStroke();
    const n = sides + 1;
    const r = pg.width / n / 4;
    const t = p.millis() * 0.001 * (sides % 3);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(sides * Math.PI / 4);
    for (let i = -n - 1; i <= n + 1; i++) {
      for (let j = -n - 1; j <= n + 1; j++) {
        pg.push();
        const dt = EasingFunctions.easeInOutCubic(t % 1) * pg.width / 2 / n;
        const dx = (i + n + 1) % 2 == Math.floor(t) % 2 ? dt : 0;
        pg.translate(pg.width / 2 / n * j + dx, pg.width / 2 / n * i);
        pg.ellipse(0, 0, r, r);
        pg.pop();
      }
    }
  }
}

class SquareGridRotate {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides } = args;
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

class SquareGrid {
  constructor({ p }) {
    this.p = p;
  }
  draw(pg, args) {
    const p = this.p
    const { col, sides } = args;
    const n = sides + 1;
    const r = pg.width / n / 4 * Math.sqrt(2);

    const t = p.millis() * 0.001;
    setColor(pg, 'background', col.bg);
    setColor(pg, 'fill', col.fg);
    pg.noStroke();
    pg.rectMode(p.CENTER);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.rotate(Math.PI / 2 * sides);
    for (let i = -n - 1; i <= n + 1; i++) {
      for (let j = -n - 1; j <= n + 1; j++) {
        pg.push();
        const dt = EasingFunctions.easeInOutCubic(t % 1) * pg.width / 2 / n;
        const dx = (i + n + 1) % 2 == Math.floor(t) % 2 ? dt : 0;
        pg.translate(pg.width / 2 / n * j + dx, pg.width / 2 / n * i);
        pg.rotate(Math.PI / 4 * sides);
        pg.rect(0, 0, r, r);
        pg.pop();
      }
    }
  }
}

const s = (p) => {
  const wipeDraws = [
    // new Blobs({ p }),
    new ClockWipe({ p }),
    new ShapeExpandWipe({ p }),
    new ShapeExpandReturn({ p }),
  ];
  const solidDraws = [
    // new Blobs({ p }),
    new SquareGridRotate({ p }),
    new SquareGrid({ p }),
    new CircleGridMove({ p }),
  ];
  const synths = {};
  const feedbackLoop = new FeedbackLoop();

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

  const history = [];
  const savedHistory = [
    "<<<n32f>>>",
    "<<<<<<n32f=>><<40p=>>>>>>",
    "<<<<<n32f=>>40<<m=+1>>>>>",
    "<<<<<n32f=>>40<<m=+2>><60~>>>>",
    "<<<n=n=<<n32f=>>40<<m=+2>><60~>>>>",
    "<<<60pn=mn=<<n32f=>>40<<m=+2>><60~>>>>",
    "<<<<60pn=mn=>><<nf=+1>>40<<m=+2>><60~>>>",
    "<<<<60pn=mn=>><<nf=+1>><<40<<m=+2>>~>>>>",
    "<60<<<pn=mn=>><<nf=+2>><<40<<m=+4>>~>>>>"
  ];
  let curHistory = 0;

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

    if (replayMode == false) {
      codeInput = p.createInput(codeBase);
      codeInput.size(p.width * 1.5);
    }

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
        if (p.frameCount % 4 == 0) {
          lastNode = node;
          node = tokens[pointer];
          execute(node);
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
              args: { wipe: wipe0 }
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

    turn.update({ t });
    turn.draw({ pg: p });

    p.loadPixels();
    let pixels = p.pixels;
    let d = p.pixelDensity();
    let meml = 68729;
    let note = p.map(freq, 20, 60, 0, width * height * d * d * 4 - meml*4);
    pixels.copyWithin(note + freq, 1, meml);
    // pixels.copyWithin(note + freq + meml*2, meml*3+1, meml*4);
    if (curSynth == '~') {
      // pixels.reverse();
    }
    p.updatePixels();
    let dt = Math.sin(t * Math.PI * 0.2) * 0.5 + 0.5;
    let du = Math.sin(t * Math.PI * 0.1) * 0.5 + 0.5;
    if (feedbackLoop.feedbackDelay) {
      feedbackLoop.feedbackDelay.delayTime.linearRampTo(p.map(dt, 0, 1, 0.1, 0.3), 1 / 60);
      feedbackLoop.effectiveBufferSize = Math.floor(p.map(du, 0, 1, 512, 2048));
    }
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
    this.bufferSize = 2048;
    this.effectiveBufferSize = this.bufferSize;
    this.amp = 0.9;
  }
  setup() {
    if (this.isSetup) return;
    this.analyser = new Tone.Analyser({
      size: this.bufferSize,
      type: 'waveform',
      smoothing: 0.0
    }
    );
    this.feedbackDelay = new Tone.FeedbackDelay(0.1, 0.5).connect(this.analyser);

    this.feedback = Tone.context.createScriptProcessor(this.bufferSize, 1, 1);
    this.feedback.onaudioprocess = (e) => {
      let a = this.analyser.getValue();
      let output = e.outputBuffer.getChannelData(0);
      for (let i = 0; i < this.bufferSize; i++) {
        output[i] = a[i % this.effectiveBufferSize] * this.amp;
      }
    }

    this.feedback.connect(this.analyser);
    this.feedback.connect(Tone.Master);
    this.isSetup = true;
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>