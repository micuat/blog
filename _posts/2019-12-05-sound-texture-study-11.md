---
layout: post
title:  "Sound Texture Study 11"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-05-sound-texture-study-11.png
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
var colorSchemes = [
  new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f"),
];

function ColorScheme(colorString) {
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
  }
  this.offset = 0;
}

ColorScheme.prototype.get = function(i) {
  i = Math.min(this.colors.length - 1, Math.max(0, i));
  return this.colors[(i + this.offset) % this.colors.length];
}

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
    if(this.started == false) {
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
    if(this.started == false) {
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
  let sine;
  let am;
  let fm;
  let saw;
  let tri;
  let square;
  let noise;

  let freq = 440;
  let pointer = 0;
  let codeInput;
  let runButton;
  let output;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = '<<<<220a>><<<=110^><<<220a>><<=110^>>2200~';
  let pastCommands = [];
  let colorShift = 1;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    sine = new p5.Oscillator(freq, 'sine');
    am = new AM();
    fm = new FM();
    saw = new p5.Oscillator(freq, 'sawtooth');
    tri = new p5.Oscillator(freq, 'triangle');
    square = new p5.Oscillator(freq, 'square');
    noise = new p5.Noise('white');

    codeInput = p.createInput(codeBase);
    codeInput.size(p.width);

    codeInput.elt.onkeyup = runButtonClicked;
  }
  
  p.mouseClicked = () => {
    runButtonClicked();
  }

  p.draw = () => {
    setColor(p, 'background', 0);
    p.noFill();
    p.translate(p.width / 2, p.height / 2);
    p.rectMode(p.CENTER);
    if (isPlaying) {
      if (pointer < tokens.length) {
        let shapeFuncs = [
          (w, x, y) => {
            p.rect(x, y, w, w);
          },
          (w, x, y) => {
            p.ellipse(x, y, w, w);
          },
          (w, x, y) => {
            p.push();
            p.translate(x, y);
            p.line(-w/2, 0, w/2, 0);
            p.line(0, -w/2, 0, w/2);
            p.pop();
          },
        ]
        if (colorShift % 2 == 0) {
          shapeFunc = 'ellipse';
        }

        let node = tokens[pointer];
        execute(node);
        if (!isNaN(node)) {
          pastCommands.push(freq);
        }
        else {
          pastCommands.push(node);
        }
        if (pastCommands.length > 15*15) pastCommands.shift();
        pointer++;

        if (!isNaN(node) && node > 1000) {
          colorShift++;
          if (colorShift > 3) colorShift = 0;
        }
        let h = p.width / 16;
        p.rectMode(p.CENTER);
        for(let i = 0; i < pastCommands.length; i++) {
          const index = (i-(p.frameCount%pastCommands.length)+pastCommands.length) % pastCommands.length;
          const command = pastCommands[index];

          if (!isNaN(command)) {
            if (index == pastCommands.length - 1)
              setColor(p, 'stroke', 1);
            else
              setColor(p, 'stroke', 2);
            p.strokeWeight(h / 8);
            if (command > 1000) {
              setColor(p, 'stroke', 3);
              shapeFuncs[2](h*4, (i%15+1-8) * h, Math.floor(i/15-7) * h);
            }
            else {
              shapeFuncs[0](h, (i%15+1-8) * h, Math.floor(i/15-7) * h);
            }
          }
          else if (command == '=') {
          }
          else {
            if (index == pastCommands.length - 1)
              setColor(p, 'stroke', 3);
            else
              setColor(p, 'stroke', 1);
            // p.text(command, 0, (i+1) * h);
            p.strokeWeight(h / 8);
            shapeFuncs[1](h, (i+1-8) * h, Math.floor(i/15) * h);
          }
        }
      } else {
        isPlaying = false;
      }
    } else {
      sine.stop();
      am.stop();
      fm.stop();
      saw.stop();
      tri.stop();
      square.stop();
      noise.stop();
      prevChar = '';
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
            sine.start();
            break;
          case 'a':
            am.start();
            break;
          case 'f':
            fm.start();
            break;
          case 'N':
            saw.start();
            break;
          case '^':
            tri.start();
            break;
          case '[':
            square.start();
            break;
          case '=':
            sine.stop();
            am.stop();
            fm.stop();
            saw.stop();
            tri.stop();
            square.stop();
            noise.stop();
            break;
          case '+':
          case '-':
          case '*':
          case '/':
          case '<':
          case '>':
            break;
          default:
            noise.start();
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
          sine.freq(freq);
          am.freq(freq);
          fm.freq(freq);
          saw.freq(freq);
          tri.freq(freq);
          square.freq(freq);
        }
      }
    }
    prevChar = t;
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>