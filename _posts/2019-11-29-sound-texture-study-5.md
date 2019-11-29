---
layout: post
title:  "Sound Texture Study 5"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-11-29-sound-texture-study-5.png
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
  let bigCount = 0;
  let bigCount2 = 0;
  let count = 0;
  let count2 = 0;
  const genFreq = () => {return Math.random() > 0.2 ? getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['3', '4'])) : 0}
  const genFreq2 = () => {return getFrequency(p.random(['A', 'B', 'C', 'D', 'E', 'F', 'G'])+p.random(['1', '2']))}
  const alen = 6;
  const rhythm = { perm: [], perm2: [], delays: [], percl: [], perch: [] };
  for(let i = 0; i < alen; i++) {
    rhythm.perm[i] = genFreq();
    rhythm.perm2[i] = genFreq2();
    rhythm.delays[i] = Math.random() > 0.7 ? 0.25 : 0;
    rhythm.percl[i] = Math.random() > 0.75;
    rhythm.perch[i] = Math.random() > 0.75;
  }
  let x = 0;
  let y = 0;
  let posl = 0;
  let posh = 0;
  let lastPosl = 0;
  let lastPosh = 0;
  p.draw = () => {
    const t = p.millis() * 0.001;

    let tFreq = 8;
    if (playing) {
      if (Math.floor(t * tFreq) - Math.floor(lastT * tFreq) > 0) {
        nodes.nm.osc.type = 'sine';
        count = (count + 1) % rhythm.perm.length;
        let f = rhythm.perm[count];
        // nodes.nm.modGain.gain.linearRampToValueAtTime(f * (+1), audioCtx.currentTime + 0.04); // shift/delay important
        // nodes.nm.osc.frequency.linearRampToValueAtTime(f, audioCtx.currentTime + 0.04 + rhythm.delays[count]/tFreq); // delay important
        // nodes.nm.amp.gain.linearRampToValueAtTime(0.1, audioCtx.currentTime + 0.04 + 0.125/tFreq + rhythm.delays[count]/tFreq);
        // nodes.nm.amp.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.04 + 0.25 / tFreq + rhythm.delays[count]/tFreq);

        lastPosl = posl;
        if(rhythm.percl[count]) {
          nodes.pulsel.play(0 + rhythm.delays[count]/tFreq);
          posl = (posl + 1) % 4;
        }
        lastPosh = posh;
        if(rhythm.perch[count]) {
          nodes.pulseh.play(0);
          posh = (posh + 1) % 4;
        }
        if(count == 0) {
          bigCount = (bigCount + 1) % 4;
        }
        if(bigCount == 0) {
          let pos = Math.floor(Math.random()*alen);
          rhythm.perm[pos] = genFreq();
          rhythm.delays[pos] = Math.random() > 0.7 ? 0.5 : 0;
          rhythm.percl[pos] = Math.random() > 0.75;
          rhythm.perch[pos] = Math.random() > 0.75;
        }
      }
      tFreq = 0.5;
      if (Math.floor(t * tFreq) - Math.floor(lastT * tFreq) > 0) {
        count2 = (count2 + 1) % rhythm.perm.length;
        let f = rhythm.perm2[count2];
        // nodes.nm2.modGain.gain.linearRampToValueAtTime(f * (+1), audioCtx.currentTime + 0.04 + 0.5/4); // shift/delay important
        // nodes.nm2.osc.frequency.linearRampToValueAtTime(f, audioCtx.currentTime + 0.04 + rhythm.delays[count]/4 + 0.5/4); // delay important

        if(count2 == 0) {
          bigCount2 = (bigCount2 + 1) % 4;
        }
        if(bigCount2 == 0) {
          let pos = Math.floor(Math.random()*rhythm.perm.length);
          rhythm.perm2[pos] = genFreq2();
        }
      }
    }
    lastT = t;

    setColor(p, 'background', 0);
    p.noStroke();
    p.rectMode(p.CENTER);
    p.translate(p.width / 2, p.height / 2);
    if(playing) {
      let W = p.width / 2;
      let w = W / 2;
      
      setColor(p, 'fill', 4)
      p.rect(0, 0, W, W);

      let x = (p.lerp(lastPosl, posl, Math.min(1, ((t / tFreq) % 1) * 2)) % 1) * p.width;
      setColor(p, 'fill', 3)
      p.push();
      p.rotate(lastPosl * Math.PI / 2);
      p.rect(x - W / 2 - w / 2, -w / 2 - W / 2, w, w);
      p.pop();

      x = (p.lerp(lastPosh, posh, Math.min(1, ((t / tFreq) % 1) * 2)) % 1) * p.width;
      setColor(p, 'fill', 1)
      p.push();
      p.rotate((lastPosh + 2) * Math.PI / 2);
      p.rect(x - W / 2 - w / 2, -w / 2 - W / 2, w, w);
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

class Mod {
  constructor() {
    let lfoHz = 16;
    
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

    this.osc = audioCtx.createOscillator();
    this.osc.type = 'sawtooth';
    this.osc.frequency.setValueAtTime(0, audioCtx.currentTime);
    this.osc.connect(this.biquadFilter);
    
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
    this.osc.start();
  }
  
  setParam(param) {
    
  }
}

class Pulse {
  constructor({type, freq, noiseFreq}) {
    this.amp = audioCtx.createGain();
    this.amp.gain.setValueAtTime(0.0, audioCtx.currentTime);
    this.amp.connect(audioCtx.destination);

    this.biquadFilter = audioCtx.createBiquadFilter();
    this.biquadFilter.type = type;
    this.biquadFilter.Q.setValueAtTime(10, audioCtx.currentTime);
    this.biquadFilter.frequency.setValueAtTime(freq, audioCtx.currentTime);
    this.biquadFilter.gain.setValueAtTime(50, audioCtx.currentTime);
    this.biquadFilter.connect(this.amp);
    
    this.osc = audioCtx.createOscillator();
    this.osc.type = 'sine';
    this.osc.frequency.setValueAtTime(noiseFreq, audioCtx.currentTime);
    this.osc.connect(this.biquadFilter);
  }

  start() {
    this.osc.start();
  }
  
  play(delay) {
    this.amp.gain.linearRampToValueAtTime(0.1, audioCtx.currentTime + 0.04 + delay);
    this.amp.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.1 + delay);
  }
  
  setParam(param) {
    
  }
}

const nodes = {
  nm: new Mod(),
  nm2: new Mod(),
  pulsel: new Pulse({noiseFreq: 1024, freq: 1800, type: 'lowpass'}),
  pulseh: new Pulse({noiseFreq: 2048, freq: 1600, type: 'lowpass'})
};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>