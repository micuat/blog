---
layout: post
title:  "Sound Texture Study 7"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-01-sound-texture-study-7.png
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
        this.bigCount = (this.bigCount + 1) % 4;
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

  const rPerm = new Rhythm({
    length: 6, freq: 8,
    generate: () => {
      return Math.random() > 0.2 ? getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['3', '4'])) : 0
    },
    execute: (a) => {
      nodes.nm.modGain.gain.linearRampToValueAtTime(a * (+1), audioCtx.currentTime + 0.04); // shift/delay important
      nodes.nm.osc.frequency.linearRampToValueAtTime(a, audioCtx.currentTime + 0.04); // delay important
    }
  });

  const rl = new Rhythm({
    length: 6, freq: 4,
    generate: () => {
      return Math.random() > 0.5;
    },
    generateDelay: () => {
      return Math.random() > 0.25 ? 0 : 0.25;
    },
    execute: (a, delay) => {
      if(a) {
        nodes.pulsel.play(delay);
      }
    }
  });
  const rh = new Rhythm({
    length: 6, freq: 8,
    generate: () => {
      return Math.random() > 0.5;
    },
    generateDelay: () => {
      return Math.random() > 0.25 ? 0 : 0.25;
    },
    execute: (a, delay) => {
      if(a) {
        nodes.pulseh.play(delay);
      }
    }
  });
  let ampl = 0;
  let amph = 0;
  p.draw = () => {
    const t = p.millis() * 0.001;
    if (playing) {
      rl.update({t});
      rh.update({t});
    }
    lastT = t;

    setColor(p, 'background', 0);
    let w = p.width / 2;
    let h = p.width / 8;
    
    ampl = p.lerp(ampl, nodes.pulsel.amp.gain.value, 0.2);
    amph = p.lerp(amph, nodes.pulseh.amp.gain.value, 0.2);

    p.noStroke();
    for(let j = 0; j < 4; j++) {
      let next = {x: 0, y: 0};
      p.push();
      p.translate(p.width / 2, 0);
      p.translate(0, h * 2 * j);
      for(let i = 0; i < 2; i++) {
        setColor(p, 'fill', i % 2 + 1);
        let angle = ampl * 10 * Math.PI / 2;
        angle += Math.PI / 4;
        if(i % 2 == 0) angle = 0;
        p.translate(next.x, next.y);
        next.x = h * Math.sin(angle);
        next.y = h * Math.cos(angle);
        p.beginShape();
        p.vertex(0, 0);
        p.vertex(w, 0);
        p.vertex(w + next.x, next.y);
        p.vertex(next.x, next.y);
        p.endShape();
      }
      p.pop();
    }
    
    h = p.width / 16;
    for(let j = 0; j < 8; j++) {
      next = {x: 0, y: 0};
      p.push();
      p.translate(0, h * 2 * j);
      for(let i = 0; i < 2; i++) {
        setColor(p, 'fill', (i+1) % 2 + 3);
        let angle = amph * 10 * Math.PI / 2;
        angle += Math.PI / 4;
        if(i % 2 == 0) angle = 0;
        p.translate(next.x, next.y);
        next.x = h * Math.sin(angle);
        next.y = h * Math.cos(angle);
        p.beginShape();
        p.vertex(0, 0);
        p.vertex(w, 0);
        p.vertex(w + next.x, next.y);
        p.vertex(next.x, next.y);
        p.endShape();
      }
      p.pop();
    }
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

class BandpassNoise {
  constructor({gain, freq, destination}) {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(gain, audioCtx.currentTime);
    this.amp.connect(destination);

    this.biquadFilter = audioCtx.createBiquadFilter();
    this.biquadFilter.type = 'bandpass';
    this.biquadFilter.Q.setValueAtTime(40, audioCtx.currentTime);
    this.biquadFilter.frequency.setValueAtTime(freq, audioCtx.currentTime);
    this.biquadFilter.connect(this.amp);
    
    const noiseLength = 2;
    const bufferSize = audioCtx.sampleRate * noiseLength;
    const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);

    let data = buffer.getChannelData(0); // get data

    // fill the buffer with noise
    for (let i = 0; i < bufferSize; i++) {
      data[i] = Math.random() * 2 - 1;
    }

    // create a buffer source for our created data
    this.noise = audioCtx.createBufferSource();
    this.noise.buffer = buffer;
    this.noise.loop = true;
    this.noise.connect(this.biquadFilter);
  }

  start() {
    this.noise.start();
  }
}

class Blow {
  constructor({freq}) {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    let f = freq;
    const fn = 2;
    const g = 15;
    this.n0 = new BandpassNoise({gain: g, freq: f, destination: this.amp});
    f *= fn;
    this.n1 = new BandpassNoise({gain: g/2, freq: f, destination: this.amp});
    f *= fn;
    this.n2 = new BandpassNoise({gain: g/4, freq: f, destination: this.amp});
    f *= fn;
    this.n3 = new BandpassNoise({gain: g/8, freq: f, destination: this.amp});
  }

  start() {
    this.n0.start();
    this.n1.start();
    this.n2.start();
  }
  
  play(delay) {
    this.amp.gain.linearRampToValueAtTime(0.1, audioCtx.currentTime + 0.1 + delay);
    this.amp.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.2 + delay);
  }
}

const nodes = {
  pulsel: new Blow({freq: 256}),
  pulseh: new Blow({freq: 2048})
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>