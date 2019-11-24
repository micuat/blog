---
layout: post
title:  "Sound Texture Study 2"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-11-24-Sound-Texture-Study-2.png
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

const s = (p) => {
  p.setup = () => {
    p.createCanvas(400, 400);
  }

  let playing = false;
  let dataArray, bufferLength;
  let ampArray = [];
  let count = 0;
  let subcount = 0;
  const perm = [];
  for(let i = 0; i < 3; i++) {
    perm[i] = getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['3', '4']));
  }
  const toffs = [];
  for(let i = 0; i < 3; i++) {
    toffs[i] = Math.random();
  }
  const setTypes = [];
  for(let i = 0; i < 3; i++) {
    setTypes[i] = Math.floor(Math.random() * 2);
  }
  p.draw = () => {
    const t = p.millis() * 0.001;

    const tFreq = 8;
    if (playing) {
      subcount+=1;
      if (Math.floor(t * tFreq) - Math.floor(lastT * tFreq) > 0) {
        count = (count + 1) % perm.length;
        let f = perm[count];
        let toff = 0;//toffs[count] / freq;
        nodes.nm.modGain.gain.linearRampToValueAtTime(f * (toffs[count]+1), audioCtx.currentTime + 0.01);
        nodes.nm.noise.frequency.linearRampToValueAtTime(f, audioCtx.currentTime + 0.01);
        if(count == 0) {
          subcount = 0;
          const pos = Math.floor(Math.random()*perm.length);
          perm[pos] = getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['3', '4']));
          toffs[pos] = Math.random();
          setTypes[pos] = Math.floor(Math.random() * 2);
        }
      }
    }
    lastT = t;

    setColor(p, 'background', 0);
    let nm = nodes.nm;
    if(playing) {
      const fmin = getFrequency('C3');
      const fmax = getFrequency('B4');
      const amp = p.map(nodes.nm.modGain.gain.value, fmin, fmax, 1, 0);
      const w = p.width / 2;
      p.translate(p.width / 2, p.height / 2);

      p.push();
      p.noStroke();
      setColor(p, 'fill', 2);
      for(let i = 0; i < 16; i++) {
        const ioff = count - (i<subcount?0:1)+3;
        const amp = p.map(perm[ioff % perm.length], fmin, fmax, 1, 0);
        const toff = toffs[ioff % perm.length];
        p.push();
        p.rotate(i / 16 * 2 * Math.PI + t);
        const r = p.width / 16;
        p.ellipse(p.width / 6 * (amp + 1), 0, r, r);
        if(toff > 0) {
          p.blendMode(p.HARD_LIGHT);
          p.rotate(1 / 16 * Math.PI * toff);
          p.ellipse(p.lerp(p.width / 6 * (amp + 1), p.width / 3, toff), 0, r, r);
        }
        p.pop();
      }
      p.pop();
    }

  }

  p.mousePressed = () => {
    if (0 <= p.mouseX && p.mouseX < p.width && 0 <= p.mouseY && p.mouseY < p.height) {
      if (playing == false) {
        playing = true;
        nodes.nm.start();
      }
    }
  }
}

// for cross browser compatibility
const AudioContext = window.AudioContext || window.webkitAudioContext;
const audioCtx = new AudioContext();

class NoiseMod {
  constructor() {
    let lfoHz = 128;
    
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.1, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.biquadFilter = audioCtx.createBiquadFilter();
    this.biquadFilter.type = "lowpass";
    this.biquadFilter.Q.setValueAtTime(10, audioCtx.currentTime);
    this.biquadFilter.frequency.setValueAtTime(1000, audioCtx.currentTime);
    this.biquadFilter.gain.setValueAtTime(25, audioCtx.currentTime);
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
//     this.noise = audioCtx.createBufferSource();
//     this.noise.buffer = buffer;
//     this.noise.loop = true;
//     this.noise.connect(this.biquadFilter);
    this.noise = audioCtx.createOscillator();
    this.noise.type = 'sawtooth';
    this.noise.frequency.setValueAtTime(1000, audioCtx.currentTime);
    this.noise.connect(this.biquadFilter);
    
    this.modGain = audioCtx.createGain();
    this.modGain.gain.value = 1;
    this.modGain.connect(this.biquadFilter.frequency);

    this.lfo = audioCtx.createOscillator();
    this.lfo.type = 'sine';
    this.lfo.frequency.setValueAtTime(lfoHz, audioCtx.currentTime);
    this.lfo.connect(this.modGain);
  }

  start() {
    this.lfo.start();
    this.noise.start();
  }
  
  setParam(param) {
    
  }
}

const nodes = {
  nm: new NoiseMod(),
  pulse: {}
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>