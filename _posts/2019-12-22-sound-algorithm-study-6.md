---
layout: post
title:  "Sound Algorithm Study 6"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-22-sound-algorithm-study-6.png
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
    this.pgbF = p.createGraphics(width, height);
    this.pgbB = p.createGraphics(width, height);
    // this.pgMaskI = p.createGraphics(width, height);

    this.bangT = 0;
    this.bangDur = 0.75;
    this.bangCycle = 0;
    this.bangParam = 0;
  }

  bang({ t, fore, back, wipe }) {
    this.backDraw = this.foreDraw;
    this.foreDraw = fore;
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

    this.pgbF.push();
    this.pgbF.blendMode(p.BLEND);
    this.pgbF.background(0);
    this.pgbF.image(this.pgF, 0, 0);
    this.pgbF.blendMode(p.MULTIPLY);
    this.pgbF.image(this.pgMask, 0, 0);

    // this.pgMaskI.blendMode(p.BLEND);
    // this.pgMaskI.background(255);
    // this.pgMaskI.blendMode(p.DIFFERENCE);
    // this.pgMaskI.image(this.pgMask, 0, 0);
    this.pgMask.filter(myp5.INVERT);

    this.pgbB.blendMode(p.BLEND);
    this.pgbB.background(0);
    this.pgbB.image(this.pgB, 0, 0);
    this.pgbB.blendMode(p.MULTIPLY);
    this.pgbB.image(this.pgMask, 0, 0);
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
      let dx = (Math.random() - 0.5) * width / 24;
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
    let dx = (Math.random() - 0.5) * width / 24;
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
const solidDraws = [
  (pg, args) => {
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
  },
  (pg, args) => {
    let { col, sides } = args;
    let n = sides + 1;
    let r = pg.width / n / 4 * Math.sqrt(2);

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
    pg.rectMode(myp5.CENTER);
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
  },
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

  let codeBase = '<<<<<40f<<+8p=-4f>>86~';
  let pastCommands = [];

  let wipe0;
  let glitch0;


  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    wipe0 = new WipeGraphics({ p });
    wipe0.foreDraw = new Drawer({ f: solidDraws[0] });
    wipe0.backDraw = new Drawer({ f: solidDraws[1] });
    wipe0.wipeDraw = new Drawer({ f: wipeDraws[0], args: { wipe: wipe0 } });

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
    codeInput.size(p.width);

    codeInput.elt.onkeyup = runButtonClicked;
  }

  p.mouseClicked = () => {
    runButtonClicked();
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
          wipe0.bang({
            t,
            fore: new Drawer({
              f: p.random(solidDraws)
            }),
            wipe: new Drawer({
              f: wipeDraws[curPattern],
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

    wipe0.update();
    wipe0.draw({ pg: glitch0.pgF });
    glitch0.draw({ pg: p });
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