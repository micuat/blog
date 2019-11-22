---
layout: post
title:  "Sound Texture Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-11-22-sound-texture-study-1.png
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
  const perm = [];
  for(let i = 0; i < 3; i++) {
    perm[i] = getFrequency(p.random(['A', 'A#', 'B', 'C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#'])+p.random(['3','5']));//Math.random() * 220 + 220;
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

    if (playing) {
      const freq = 8;
      if (Math.floor(t * freq) - Math.floor(lastT * freq) > 0) {
        let f = perm[count];
        let toff = 0;//toffs[count] / freq;
        if(true || setTypes[count] == 0) {
          nodes.fm.osc.frequency.setValueAtTime(f, audioCtx.currentTime + toff);
        }
        else {
          nodes.fm.osc.frequency.linearRampToValueAtTime(f, audioCtx.currentTime + toff);
        }
        count = (count + 1) % perm.length;
        if(count == 0) {
          nodes.fm.biquadFilter.frequency.setValueAtTime(p.random(500, 2000), audioCtx.currentTime);
          nodes.fm.carrier.frequency.linearRampToValueAtTime(p.random([2, 16, 32]), audioCtx.currentTime + 0.1);
          const pos = Math.floor(Math.random()*perm.length);
          perm[pos] = getFrequency(p.random(['A', 'A#', 'B', 'C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#'])+p.random(['3','5']));//Math.random() * 110 + 220;
          toffs[pos] = Math.random();
          setTypes[pos] = Math.floor(Math.random() * 2);
        }
      }
    }
    lastT = t;

    setColor(p, 'background', 0);
    let fm = nodes.fm;
    if (fm.analyser != undefined) {
      p.push();
      if (dataArray == undefined) {
        bufferLength = fm.analyser.frequencyBinCount;
        dataArray = new Uint8Array(bufferLength);
      }
      fm.analyser.getByteTimeDomainData(dataArray);
      let avg = 0;
      for (let i = 0; i < bufferLength; i++) {
        let v = dataArray[i] / 128.0;
        avg += Math.abs(v * 0.5 - 0.5);
      }
      avg /= bufferLength * 5;
      avg *= fm.amp.gain.value * 10;
      ampArray.push(avg);
      if (ampArray.length > 5) ampArray.shift();
      a = arr => arr.reduce((a, b) => a + b, 0) / arr.length;
      avg = a(ampArray);
      p.noStroke();
      p.translate(p.width / 2, p.height / 2);
      p.rectMode(p.CENTER);
      for (let i = -4; i <= 4; i++) {
        setColor(p, 'stroke', count % 3 + 1);
        p.noFill();
        // p.rect(i / 8 * p.width, 0, avg * p.width, p.height);
        p.beginShape();
        for (let j = 0; j < 100; j++) {
          let x = avg * p.random(-1, 1) * p.width + i / 8 * p.width;
          let y = p.map(j, 0, 99, -p.height / 2, p.height / 2);
          p.vertex(x, y);
        }
        p.endShape();
      }
      p.pop();
    }
  }

  p.mousePressed = () => {
    if (0 <= p.mouseX && p.mouseX < p.width && 0 <= p.mouseY && p.mouseY < p.height) {
      if (playing == false) {
        playing = true;
        nodes.fm.start();
      }
    }
  }
}

// for cross browser compatibility
const AudioContext = window.AudioContext || window.webkitAudioContext;
const audioCtx = new AudioContext();

class FreqMod {
  constructor() {
    let pulseHz = 440;
    let lfoHz = 0.1;
    let carrierHz = 4;
    
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.1, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.biquadFilter = audioCtx.createBiquadFilter();
    this.biquadFilter.type = "lowpass";
    this.biquadFilter.frequency.setValueAtTime(1000, audioCtx.currentTime);
    this.biquadFilter.gain.setValueAtTime(25, audioCtx.currentTime);
    this.biquadFilter.connect(this.amp);
    
    this.carAmp = audioCtx.createGain();
    this.carAmp.gain.setValueAtTime(0.1, audioCtx.currentTime);
    this.carAmp.connect(this.biquadFilter);

    this.osc = audioCtx.createOscillator();
    this.osc.type = 'sawtooth';
    this.osc.frequency.setValueAtTime(pulseHz, audioCtx.currentTime);
    this.osc.connect(this.carAmp);

    this.carGain = audioCtx.createGain();
    this.carGain.gain.value = 0.1;
    this.carGain.connect(this.carAmp.gain);

    this.carrier = audioCtx.createOscillator();
    this.carrier.type = 'square';
    this.carrier.frequency.setValueAtTime(carrierHz, audioCtx.currentTime);
    this.carrier.connect(this.carGain);

    this.modGain = audioCtx.createGain();
    this.modGain.gain.value = 10;
    this.modGain.connect(this.osc.frequency);

    this.lfo = audioCtx.createOscillator();
    this.lfo.type = 'sine';
    this.lfo.frequency.setValueAtTime(lfoHz, audioCtx.currentTime);
    this.lfo.connect(this.modGain);

    this.analyser = audioCtx.createAnalyser();
    this.analyser.fftSize = 1024;
    this.carGain.connect(this.analyser);
  }

  start() {
    this.lfo.start();
    this.carrier.start();
    this.osc.start();
  }
  
  setParam(param) {
    
  }
}

const nodes = {
  fm: new FreqMod(),
  pulse: {}
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>