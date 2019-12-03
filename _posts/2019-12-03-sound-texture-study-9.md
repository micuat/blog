---
layout: post
title:  "Sound Texture Study 9"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-03-sound-texture-study-9.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start. Better-oscillator taken from [here](https://github.com/Flarp/better-oscillator).

Phase modulation idea thanks to Tobias Hartmann.

<script>
var getFrequency = function (note) {
    var notes = ['A', 'A#', 'B', 'C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#'],
        octave,
        keyNumber;

    if (note.length === 3) {
        octave = note.charAt(2);
    } else {
        octave = note.charAt(1);
    }

    keyNumber = notes.indexOf(note.slice(0, -1));

    if (keyNumber < 3) {
        keyNumber = keyNumber + 12 + ((octave - 1) * 12) + 1; 
    } else {
        keyNumber = keyNumber + ((octave - 1) * 12) + 1; 
    }

    // Return frequency of note
    return 440 * Math.pow(2, (keyNumber- 49) / 12);
};

var colorSchemes = [
  new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f"),
];

function ColorScheme(colorString) {
  this.colors = [];
  {
    let cc = colorString.split("/");
    let cs = cc[cc.length - 1].split("-");
    for (let i in cs) {
      let r = parseInt("0x" + cs[i].substring(0, 2));
      let g = parseInt("0x" + cs[i].substring(2, 4));
      let b = parseInt("0x" + cs[i].substring(4, 6));
      this.colors.push({ r: r, g: g, b: b });
    }
  }
  this.offset = 0;
}

ColorScheme.prototype.get = function (i) {
  i = Math.min(this.colors.length - 1, Math.max(0, i));
  return this.colors[(i + this.offset) % this.colors.length];
}

function setColor(parent, func, index, alpha) {
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[0].get(index).r, colorSchemes[0].get(index).g, colorSchemes[0].get(index).b, alpha);
}

class Rhythm {
  constructor({length, freq, generate, execute, generateDelay}) {
    this.generate = generate;
    this.generateDelay = generateDelay;
    this.execute = execute;

    this.length = length;
    this.queue = [];
    for(let i = 0; i < length; i++) {
      this.queue[i] = this.generate();
    }
    if(this.generateDelay != undefined) {
      this.queueDelay = [];
      for(let i = 0; i < length; i++) {
        this.queueDelay[i] = this.generateDelay();
      }
    }
    this.freq = freq;

    this.lastT = 0;
    this.count = 0;
    this.bigCount = 0;
  }
  
  update({t}) {
    const freq = this.freq;
    const delay = this.generateDelay != undefined ? this.queueDelay[this.count] / freq : 0;
    if (Math.floor(t * freq) - Math.floor(this.lastT * freq)) {
      this.execute(this.queue[this.count], delay);
      this.count = (this.count + 1) % this.length;
      if (this.count == 0) {
        this.bigCount = (this.bigCount + 1) % 2;
        if (this.bigCount == 0) {
          const pos = Math.floor(Math.random() * this.length);
          this.queue[pos] = this.generate();
        }
      }
    }
    this.lastT = t;
  }
}

const s = (p) => {
  p.setup = () => {
    p.createCanvas(400, 400);
  }

  let playing = false;

  const rh = new Rhythm({
    length: 6, freq: 8,
    generate: () => {
      return Math.random() > 0.2 ? getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['3', '4'])) : 0
    },
    generateDelay: () => {
      return Math.random() > 0.25 ? 0 : 0.25;
    },
    execute: (a, delay) => {
      if(nodes.pm.osc != undefined) {
        nodes.pm.osc.parameters.get('frequency').value = a;
        nodes.pm.play(delay);
      }
    }
  });
  let amph = 0;
  p.draw = () => {
    const t = p.millis() * 0.001;
    if (playing) {
      rh.update({t});
    }
    lastT = t;

    setColor(p, 'background', 0);
    
    amph = p.lerp(amph, nodes.pm.amp.gain.value, 0.2);

    p.noFill();
    p.translate(p.width / 2, p.height / 2);
    setColor(p, 'stroke', 3);
    let n = 64;
    p.beginShape();
    for (let i = 0; i < n; i++) {
      let f = nodes.pm.osc ? nodes.pm.osc.parameters.get('frequency').value : 0;
      let a = Math.sin(i / n * f / 4 + t) * amph;
      let r = p.width / 4 + p.width / 2 * a;
      let th = i / n * 2 * Math.PI;
      let x = r * Math.cos(th);
      let y = r * Math.sin(th);
      p.vertex(x, y);
      p.ellipse(x, y, p.width / 64, p.width / 64)
    }
    p.endShape(p.CLOSE);
  }

  p.mousePressed = () => {
    if (0 <= p.mouseX && p.mouseX < p.width && 0 <= p.mouseY && p.mouseY < p.height) {
      if (playing == false) {
        playing = true;
        for(const key in nodes) {
          nodes[key].start();
        }
      }
    }
  }
}

// for cross browser compatibility
const AudioContext = window.AudioContext || window.webkitAudioContext;
const audioCtx = new AudioContext();

class PhaseMod {
  constructor({gain, freq}) {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(gain, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.lfo = audioCtx.createOscillator();
    this.lfo.type = 'sine';
    this.lfo.frequency.value = 32;
  }

  async start() {
    await audioCtx.audioWorklet.addModule('{{ site.baseurl }}/assets/js/better-oscillator.js');
    this.osc = new AudioWorkletNode(audioCtx, 'better-oscillator');
    this.osc.connect(this.amp);
    this.lfo.connect(this.osc.parameters.get('phase'));
    this.lfo.start();
  }
  
  play(delay) {
    this.amp.gain.linearRampToValueAtTime(0.1, audioCtx.currentTime + 0.1 + delay);
    this.amp.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.2 + delay);
  }

}

const nodes = {
  pm: new PhaseMod({gain: 0.1, freq: 2048})
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>