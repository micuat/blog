---
layout: post
title:  "Sound Algorithm Study 5"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-19-sound-algorithm-study-5.png
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
  constructor({p, pgF, pgB}) {
    this.p = p;
    this.pgF = pgF == undefined ? p.createGraphics(width, height) : pgF;
    this.pgB = pgB == undefined ? p.createGraphics(width, height) : pgB;
    this.pgMask = p.createGraphics(width, height);
    this.pgbF = p.createGraphics(width, height);
    this.pgbB = p.createGraphics(width, height);
    this.pgMaskI = p.createGraphics(width, height);
  }
  
  update() {
    let p = this.p;

    this.pgbF.push();
    this.pgbF.blendMode(p.BLEND);
    this.pgbF.background(0);
    this.pgbF.image(this.pgF, 0, 0);
    this.pgbF.blendMode(p.MULTIPLY);
    this.pgbF.image(this.pgMask, 0, 0);

    this.pgMaskI.blendMode(p.BLEND);
    this.pgMaskI.background(255);
    this.pgMaskI.blendMode(p.DIFFERENCE);
    this.pgMaskI.image(this.pgMask, 0, 0);

    this.pgbB.blendMode(p.BLEND);
    this.pgbB.background(0);
    this.pgbB.image(this.pgB, 0, 0);
    this.pgbB.blendMode(p.MULTIPLY);
    this.pgbB.image(this.pgMaskI, 0, 0);
  }
  
  draw(pg) {
    let p = this.p;

    pg.image(this.pgbB, 0, 0);
    pg.blendMode(p.ADD);
    pg.image(this.pgbF, 0, 0);
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

  let codeBase = '<<<<<40f<<+8f-2ff>>86~';
  let pastCommands = [];
  let colorShift = 1;

  let pg0;
  let mix0, mixL, mixP;
  
  let t = 0;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    mix0 = new MixGraphics({p});
    mixL = new MixGraphics({p});
    mixP = new MixGraphics({p});

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
            // hiCount++;
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
  let bangDur = 0.75;
  let bangCycle = 0;
  let bangParam = 0;
  const bang = () => {
    bangT = t;
    bangCycle = (bangCycle + 1) % 2;
    bangParam = Math.floor(p.random(4));
  }
  const wipeDraws = [
    (pg) => { // stripes
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      setColor(pg, 'background', 0);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.noStroke();
      setColor(pg, 'fill', 2);
      // pg.rotate(Math.PI * 0.25 * hiCount);
      let n = bangParam;
      let r = pg.width / 1.4 / n;
      pg.rectMode(p.CENTER);
      for (let i = -n; i <= n; i++) {
        pg.rect(i * r, 0, p.lerp(0, r, EasingFunctions.easeInOutCubic(tw)), pg.height * 2);
      }
      pg.pop();
    },
    (pg) => { // circle expand
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      setColor(pg, 'background', 0);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.noStroke();
      setColor(pg, 'fill', 2);
      let n = bangParam;
      let r = p.lerp(0, pg.width * 1.42, EasingFunctions.easeInOutCubic(tw)) / (n * 2 + 1) * 2;
      for(let i = -n; i <= n; i++) {
        for(let j = -n; j <= n; j++) {
          pg.ellipse(pg.width / 2 / n * j, pg.width / 2 / n * i, r);
        }
      }
      pg.pop();
    },
    (pg) => { // shape expand
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
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
      pg.pop();
    },
    (pg) => { // clock wipe
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
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
      pg.pop();
    },
    (pg) => { // horizontal box push
      pg.push();
      const tw = p.constrain((t - bangT) / bangDur, 0, 1);
      setColor(pg, 'background', 0);
      pg.noStroke();
      setColor(pg, 'fill', 2);
      pg.translate(pg.width / 2, pg.height / 2);
      pg.rotate(bangParam / 2 * Math.PI);
      pg.translate(-pg.width / 2, -pg.height / 2);
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
    if (curSynth == 'f') x = 0.03;
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
      setColor(pg, 'background', 2);
      setColor(pg, 'fill', 4);
      pg.noStroke();
      pg.translate(pg.width / 2, pg.height / 2);
      blobFunc(pg, pg.width / 3);
      pg.pop();
    },
    (pg) => {
      pg.push();
      setColor(pg, 'background', 2);
      setColor(pg, 'fill', 4);
      pg.noStroke();
      waveFunc(pg);
      pg.pop();
    },
  ];
  const lfoMaskDraws = [
    (pg) => {
      pg.push();
      setColor(pg, 'background', 0);
      setColor(pg, 'fill', 2);
      pg.noStroke();
      pg.rectMode(p.CENTER);
      let n = 3;
      let r = pg.width / n;
      for (let i = 0; i < n; i++) {
        for (let j = 0; j < n; j++) {
          pg.push();
          pg.translate((j+0.5) * r, (i+0.5) * r);
          let th = (t + i + j) % 5;
          if(th > 4) th = 4;
          th = EasingFunctions.easeInOutCubic(th / 4);
          pg.rotate(th * Math.PI)
          pg.rect(0, 0, r*Math.cos(th * Math.PI), r*Math.cos(th * Math.PI));
          pg.pop();
        }
      }
      pg.pop();
    },
    (pg) => {
      pg.push();
      setColor(pg, 'background', 0);
      setColor(pg, 'fill', 2);
      pg.noStroke();
      let r = pg.width / 1;
      let n = 4;
      let w = r / n;
      for(let i = 0; i < n; i++) {
        pg.rect(w*i, 0, w, r * (EasingFunctions.easeInOutCubic(0.5+0.5*Math.sin((t+i/n*2)) * Math.PI / 2)));
      }
      pg.pop();
    },
  ];

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

    backDraws[0](mixL.pgB);
    setColorMode = 1;
    backDraws[1](mixL.pgF);
    setColorMode = 2;
    lfoMaskDraws[0](mixL.pgMask);
    setColorMode = 0;
    mixL.update();

    setColorMode = 1;
    backDraws[0](mixP.pgB);
    maskDraw(mixP.pgF);
    setColorMode = 2;
    lfoMaskDraws[1](mixP.pgMask);
    setColorMode = 0;
    mixP.update();

    if(bangCycle == 0) {
      mixL.draw(mix0.pgB);
      mixP.draw(mix0.pgF);
    }
    else {
      mixP.draw(mix0.pgB);
      mixL.draw(mix0.pgF);
    }
    
    setColorMode = 2;
    wipeDraws[curPattern](mix0.pgMask);
    setColorMode = 0;

    mix0.update();

    mix0.draw(p);
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