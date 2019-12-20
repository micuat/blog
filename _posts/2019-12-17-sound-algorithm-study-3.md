---
layout: post
title:  "Sound Algorithm Study 3"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-17-sound-algorithm-study-3.png
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

const width = 400;
const height = 400;

class MixGraphics {
  constructor(p) {
    this.p = p;
    this.pg = p.createGraphics(width, height);
    this.pgb0 = p.createGraphics(width, height);
    this.pgb1 = p.createGraphics(width, height);
    this.invertPg = p.createGraphics(width, height);
  }
  
  update(pg0, pg1) {
    let p = this.p;

    this.pgb0.push();
    this.pgb0.blendMode(p.BLEND);
    this.pgb0.background(0);
    this.pgb0.image(pg0, 0, 0);
    this.pgb0.blendMode(p.MULTIPLY);
    this.pgb0.image(this.pg, 0, 0);

    this.invertPg.blendMode(p.BLEND);
    this.invertPg.background(255);
    this.invertPg.blendMode(p.DIFFERENCE);
    this.invertPg.image(this.pg, 0, 0);

    this.pgb1.blendMode(p.BLEND);
    this.pgb1.background(0);
    this.pgb1.image(pg1, 0, 0);
    this.pgb1.blendMode(p.MULTIPLY);
    this.pgb1.image(this.invertPg, 0, 0);
  }
  
  draw(pg) {
    let p = this.p;

    pg.image(this.pgb1, 0, 0);
    pg.blendMode(p.ADD);
    pg.image(this.pgb0, 0, 0);
    pg.blendMode(p.BLEND);
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

  let codeBase = '<<<<1f<<+2<[=>>>90~><1f<<+2<[=>>>92~';
  let pastCommands = [];
  let colorShift = 1;

  let pg0, pg1, pg2, pg3, pgex;
  let mix0, mix1;
  
  let t = 0;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    pg0 = p.createGraphics(400, 400);
    pg1 = p.createGraphics(400, 400);
    pg2 = p.createGraphics(400, 400);
    pg3 = p.createGraphics(400, 400);
    pgex = p.createGraphics(400, 400);
    mix0 = new MixGraphics(p);
    mix1 = new MixGraphics(p);

    setColorMode = 1;
    patternDraw(mix1.pg);
    pgex.background(255);
    pgex.blendMode(p.MULTIPLY);
    pgex.image(mix1.pg, 0, 0);
    pgex.filter(p.BLUR, 7)
    setColorMode = 0;

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

  const maskDraw = (pg) => {
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
        if (command > 80) {
          if (!isNaN(node) && node > 80) {
            hiCount++;
          }
          setColor(pg, 'stroke', 1);
          shapeFuncs[2](pg, h * 4, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
        } else {
          shapeFuncs[0](pg, h, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
        }
      } else if (command == '=') {} else {
        if (index == pastCommands.length - 1) {
          setColor(pg, 'fill', 2);
          pg.noStroke();
        } else {
          setColor(pg, 'fill', 1);
          pg.noStroke();
        }
        pg.strokeWeight(h / 8);
        shapeFuncs[1](pg, h / 2, (i % 15 + 1 - 8) * h, Math.floor(i / 15 - 7) * h);
      }
    }
    pg.pop();
  }
  const patternDraw = (pg) => {
    pg.push();
    setColor(pg, 'background', 0);
    pg.translate(pg.width / 2, pg.height / 2);
    pg.noStroke();
    setColor(pg, 'fill', 2);
    pg.rotate(Math.PI * 0.25);
    let n = 4;
    let r = pg.width / 1.4 / n;
    pg.rectMode(p.CENTER);
    for (let i = -n; i <= n; i++) {
      pg.rect(i * r, 0, p.lerp(0, r, 0.5), pg.height * 2);
    }
    pg.pop();
  }
  let bangT = 0;
  let bangDur = 1;
  let bangCycle = 0;
  const bang = () => {
    bangT = t;
    bangCycle = (bangCycle + 1) % 2;
  }
  const wipeDraws = [
    (pg) => { // stripes
      pg.push();
      let mode;
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      if (bangCycle == 0) {
        mode = [0, 2];
      } else {
        mode = [2, 0];
      }
      setColor(pg, 'background', mode[0]);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.noStroke();
      setColor(pg, 'fill', mode[1]);
      pg.rotate(Math.PI * 0.25 * hiCount);
      let n = 4;
      let r = pg.width / 1.4 / n;
      pg.rectMode(p.CENTER);
      for (let i = -n; i <= n; i++) {
        pg.rect(i * r, 0, p.lerp(0, r, EasingFunctions.easeInOutCubic(tw)), pg.height * 2);
      }
      pg.pop();
    },
    (pg) => { // circle expand
      pg.push();
      let mode;
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      if (bangCycle == 0) {
        mode = [0, 2];
      } else {
        mode = [2, 0];
      }
      setColor(pg, 'background', mode[0]);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.noStroke();
      setColor(pg, 'fill', mode[1]);
      let r = p.lerp(0, pg.width * 1.41, EasingFunctions.easeInOutCubic(tw));
      pg.ellipse(0, 0, r);
      pg.pop();
    },
    (pg) => { // vertical wipe
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      if (bangCycle == 0) {
        mode = [0, 2];
      } else {
        mode = [2, 0];
      }
      setColor(pg, 'background', mode[0]);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.noStroke();
      setColor(pg, 'fill', mode[1]);
      let rate = EasingFunctions.easeInOutCubic(tw);
      let n = 128;
      let r = pg.width;
      pg.beginShape();
      pg.vertex(0, 0);
      for (let i = 0; i <= n; i++) {
        let theta = -i / n * Math.PI * 2 * rate - Math.PI / 2;
        let x = r * Math.cos(theta);
        let y = r * Math.sin(theta);
        pg.vertex(x, y);
      }
      pg.vertex(0, 0);
      pg.endShape();
      pg.pop();
    },
    (pg) => { // horizontal box push
      pg.push();
      let mode;
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      if (bangCycle == 0) {
        mode = [0, 2];
      } else {
        mode = [2, 0];
      }
      setColor(pg, 'background', mode[0]);
      pg.noStroke();
      setColor(pg, 'fill', mode[1]);
      let r = p.lerp(0, pg.width, EasingFunctions.easeInOutCubic(tw));
      pg.rect(0, 0, r, pg.height);
      pg.pop();
    },
  ];
  const blobFunc = (p, R) => {
    p.beginShape();
    const n = 128;
    let x = 0;
    // if (curSynth == 'f' && freq < 11000) x = 0.2;
    x = 0.2;
    for (let i = 0; i < n; i++) {
      const th = i / n * 2 * Math.PI;
      let r = R * (1 + x * Math.random());
      p.vertex(r * Math.cos(th), r * Math.sin(th));
    }
    p.endShape(p.CLOSE);
  }
  const waveFunc = (p) => {
    p.beginShape();
    const n = 128;
    let x = 0;
    if (curSynth == 'f' && freq < 11000) x = 0.02;
    p.vertex(0, p.height);
    for (let i = 0; i <= n; i++) {
      const th = i / n * 8 * Math.PI + p.millis();
      let r = x * (Math.sin(th));
      p.vertex(i / n * p.width, p.height * (0.5 + r));
    }
    p.vertex(p.width, p.height);
    p.endShape(p.CLOSE);
  }
  const backDraws = [
    (pg) => {
      pg.push();
      setColor(pg, 'background', 3);
      setColor(pg, 'fill', 1);
      pg.strokeWeight(pg.width / 64);
      pg.noStroke();
      pg.translate(pg.width / 2, pg.height / 2);
      blobFunc(pg, pg.width / 3);
      pg.pop();
    },
    (pg) => {
      pg.push();
      setColor(pg, 'background', 3);
      setColor(pg, 'fill', 1);
      pg.strokeWeight(pg.width / 64);
      pg.noStroke();
      waveFunc(pg);
      pg.pop();
    },
  ]

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
          bang();          
          curPattern = (curPattern + 1) % wipeDraws.length;
        }
      } else {
        isPlaying = false;
      }
    } else {
      for (const key in synths) {
        synths[key].stop();
      }
      prevChar = '';
    }

    setColorMode = 0;

    backDraws[0](pg1);
    maskDraw(pg0);
    setColorMode = 1;
    wipeDraws[curPattern](mix0.pg);
    setColorMode = 0;

    mix0.update(pg0, pg1);

    mix0.draw(pg3);
  
    wipeDraws[(curPattern+1)%wipeDraws.length](pg2);

    mix1.update(pg3, pg2);

    // mix1.draw(p);
    p.image(mix1.pgb0, 0, 0);
    p.blendMode(p.MULTIPLY);
    p.image(pgex, 0, 0);
    p.blendMode(p.ADD);
    p.image(mix1.pgb1, 0, 0);
    p.blendMode(p.BLEND);
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

        // if (freq == 0) freq = p.random(11000);

        let f = midiToFreq(freq);
        if (isNaN(f) == false && f < 1e5) {
          for (const key in synths) {
            if (key != 'n') {
              synths[key].freq(f);
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