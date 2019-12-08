---
layout: post
title:  "Sound Texture Study 13"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-08-sound-texture-study-13.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start.

<script>
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
];

function setColor(parent, func, index, alpha) {
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[0].get(index).r, colorSchemes[0].get(index).g, colorSchemes[0].get(index).b, alpha);
}

// for cross browser compatibility
const AudioContext = window.AudioContext || window.webkitAudioContext;
const audioCtx = new AudioContext();

class AM {
  constructor() {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.preAmp = audioCtx.createGain();
    this.preAmp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.preAmp.connect(this.amp);

    this.osc = audioCtx.createOscillator();
    this.osc.type = 'sine';
    this.osc.frequency.setValueAtTime(0, audioCtx.currentTime);
    this.osc.connect(this.preAmp);

    this.modGain = audioCtx.createGain();
    this.modGain.gain.value = 0;
    this.modGain.connect(this.preAmp.gain);

    this.lfo = audioCtx.createOscillator();
    this.lfo.type = 'sine';
    this.lfo.frequency.setValueAtTime(16, audioCtx.currentTime);
    this.lfo.connect(this.modGain);

    this.started = false;
  }

  start() {
    if (this.started == false) {
      this.osc.start();
      this.lfo.start();
      this.started = true;
    }
    this.modGain.gain.value = 1;
    this.amp.gain.setValueAtTime(0.1, audioCtx.currentTime);
  }

  freq(f) {
    if (isNaN(f)) return;
    this.osc.frequency.setValueAtTime(f, audioCtx.currentTime);
  }

  stop() {
    this.preAmp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.amp.gain.setValueAtTime(0.0, audioCtx.currentTime);
  }
}

class FM {
  constructor() {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.biquadFilter = audioCtx.createBiquadFilter();
    this.biquadFilter.type = 'lowpass';
    this.biquadFilter.Q.setValueAtTime(1, audioCtx.currentTime);
    this.biquadFilter.frequency.setValueAtTime(0, audioCtx.currentTime);
    this.biquadFilter.gain.setValueAtTime(50, audioCtx.currentTime);
    this.biquadFilter.connect(this.amp);

    this.osc = audioCtx.createOscillator();
    this.osc.type = 'sine';
    this.osc.frequency.setValueAtTime(0, audioCtx.currentTime);
    this.osc.connect(this.biquadFilter);

    this.modGain = audioCtx.createGain();
    this.modGain.gain.value = 100;
    this.modGain.connect(this.osc.frequency);

    this.lfo = audioCtx.createOscillator();
    this.lfo.type = 'sine';
    this.lfo.frequency.setValueAtTime(32, audioCtx.currentTime);
    this.lfo.connect(this.modGain);

    this.started = false;
  }

  start() {
    if (this.started == false) {
      this.osc.start();
      this.lfo.start();
      this.started = true;
    }
    this.amp.gain.setValueAtTime(0.1, audioCtx.currentTime);
  }

  freq(f) {
    if (isNaN(f)) return;
    this.biquadFilter.frequency.setValueAtTime(f * 2, audioCtx.currentTime);
    this.osc.frequency.setValueAtTime(f, audioCtx.currentTime);
  }

  stop() {
    this.amp.gain.setValueAtTime(0, audioCtx.currentTime);
  }
}

const s = (p) => {
  const synths = {};

  let freq = 440;
  let pointer = 0;
  let codeInput;
  let runButton;
  let output;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = '<<<<440f<<+40>>><440f<<<+20>>>n=1800~';
  let pastCommands = [];
  let colorShift = 1;

  let pg0, pg1, pg2;
  let pgb0, pgb1;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    pg0 = p.createGraphics(400, 400);
    pg1 = p.createGraphics(400, 400);
    pg2 = p.createGraphics(400, 400);
    pgb0 = p.createGraphics(400, 400);
    pgb1 = p.createGraphics(400, 400);

    synths['~'] = new p5.Oscillator(freq, 'sine');
    synths['a'] = new AM();
    synths['f'] = new FM();
    synths['N'] = new p5.Oscillator(freq, 'sawtooth');
    synths['^'] = new p5.Oscillator(freq, 'triangle');
    synths['['] = new p5.Oscillator(freq, 'square');
    synths['n'] = new p5.Noise('white');

    codeInput = p.createInput(codeBase);
    codeInput.size(p.width);

    codeInput.elt.onkeyup = runButtonClicked;
  }

  p.mouseClicked = () => {
    runButtonClicked();
  }

  let hiCount = 0;

  const patternDraw = (pg) => {
    pg.push();
    setColor(pg, 'background', 0);
    pg.translate(p.width / 2, p.height / 2);
    let shapeFuncs = [
      (p, w, x, y) => {
        p.rect(x, y, w, w);
      },
      (p, w, x, y) => {
        p.ellipse(x, y, w, w);
      },
      (p, w, x, y) => {
        p.push();
        p.translate(x, y);
        p.line(-w / 2, 0, w / 2, 0);
        p.line(0, -w / 2, 0, w / 2);
        p.pop();
      },
    ]

    if (!isNaN(node)) {
      pastCommands.push(freq);
    } else {
      pastCommands.push(node);
    }
    if (pastCommands.length > 15 * 15) pastCommands.shift();
    pointer++;

    if (!isNaN(node) && node > 1000) {
      colorShift++;
      if (colorShift > 3) colorShift = 0;
    }
    let h = p.width / 16;
    pg.rectMode(p.CENTER);
    for (let i = 0; i < pastCommands.length; i++) {
      const index = (i - (p.frameCount % pastCommands.length) + pastCommands.length) % pastCommands.length;
      const command = pastCommands[index];

      if (!isNaN(command)) {
        if (index == pastCommands.length - 1) {
          setColor(pg, 'fill', 1);
          pg.noStroke();
        } else {
          setColor(pg, 'fill', 2);
          pg.noStroke();
        }
        pg.strokeWeight(h / 8);
        if (command > 1000) {
          if (!isNaN(node) && node > 1000) {
            hiCount++;
          }
          setColor(pg, 'stroke', 3);
          shapeFuncs[2](pg, h * 4, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
        } else {
          shapeFuncs[0](pg, h, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
        }
      } else if (command == '=') {} else {
        if (index == pastCommands.length - 1) {
          setColor(pg, 'fill', 3);
          pg.noStroke();
        } else {
          setColor(pg, 'fill', 1);
          pg.noStroke();
        }
        // p.text(command, 0, (i+1) * h);
        pg.strokeWeight(h / 8);
        shapeFuncs[1](pg, h, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
      }
    }
    pg.pop();
  }
  const maskDraw = (pg) => {
    pg.push();
    pg.background(0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    pg.fill(255);
    pg.rotate(Math.PI / 4 * (hiCount * 2 + 1));
    let n = (hiCount % 4) + 1;
    let r = pg.width / 1.4 / n;
    pg.rectMode(p.CENTER);
    for (let i = -n; i <= n; i++) {
      pg.rect(i * r, 0, r / 2, pg.height * 2);
    }
    pg.pop();
  }
  const blendDraw = (pg) => {
    pg.push();
    pg.blendMode(p.BLEND);
    pg.background(0);
    pg.image(pg0, 0, 0);
    pg.blendMode(p.MULTIPLY);
    pg.image(pg1, 0, 0);
  }
  const blobFunc = (p, R) => {
    p.beginShape();
    const n = 128;
    let x = 0;
    if (curSynth == 'f' && freq < 11000) x = 0.1;
    for (let i = 0; i < n; i++) {
      const th = i / n * 2 * Math.PI;
      let r = R * (1 + x * Math.random());
      p.vertex(r * Math.cos(th), r * Math.sin(th));
    }
    p.endShape();
  }
  const backDraw = (pg) => {
    pg.push();
    setColor(pg, 'background', 1);
    setColor(pg, 'fill', 2);
    pg.noStroke();
    pg.translate(pg.width / 2, pg.height / 2);
    blobFunc(pg, pg.width / 3);
    pg.pop();
  }

  let node;
  let curSynth;

  p.draw = () => {
    const t = p.millis() * 0.001;

    if (isPlaying) {
      if (pointer < tokens.length) {
        node = tokens[pointer];
        execute(node);
      } else {
        isPlaying = false;
      }
    } else {
      for (const key in synths) {
        synths[key].stop();
      }
      prevChar = '';
    }

    backDraw(pg2);
    patternDraw(pg0);
    maskDraw(pg1);
    blendDraw(pgb0);

    pgb1.blendMode(p.BLEND);
    pgb1.background(0);
    pgb1.image(pg2, 0, 0);
    pgb1.blendMode(p.DIFFERENCE);
    pgb1.image(pg1, 0, 0);
    
    p.background(0);
    p.blendMode(p.BLEND);
    p.image(pgb1, 0, 0);
    p.blendMode(p.LIGHTEST);
    p.image(pgb0, 0, 0);
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
            curSynth = t;
            synths[t].start();
            break;
          case '=':
            curSynth = '';
            for (const key in synths) {
              synths[key].stop();
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
            synths.n.start();
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

        if (freq == 0) freq = p.random(11000);

        if (isNaN(freq) == false) {
          for (const key in synths) {
            if (key != 'n') {
              synths[key].freq(freq);
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