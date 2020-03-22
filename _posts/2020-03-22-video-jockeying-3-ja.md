---
layout: post
title:  "VJ について 3"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound, essay, japanese ]
image: assets/images/2020-03-22-video-jockeying-3-ja.png
description: "スケッチ"
featured: true
comments: true
p5: true
tonejs: true
---

[前回]({{ site.baseurl }}/video-jockeying-2-ja)のスケッチにオシレータを追加しました。クリックをすると再生と一時停止を切り替えられます。

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

今回の変更点は AMCJ の2月の回に触れていた Ableton のデバイス Razor にインスパイアされたオシレータを追加しました。それに伴い Stick クラスを派生させて、オシレータにあわせて波形が乗るようにしましたが、実際の波形とは関係ないサイン波を表示しています。音の終わりに絵がうまく合っていないので明日以降変更したいと思っていますが、細かい調整の前にまずは基本的な機能追加をするべきか迷っています。

{% highlight javascript %}
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

// https://gist.github.com/gre/1650294/
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t * t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t * (2 - t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t * t * t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t) * t * t + 1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t * t * t * t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1 - (--t) * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t * t * t * t * t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1 + (--t) * t * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuint: function (t) { return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t },
}

const width = 400;
const height = 400;
const bpm = 80 * 4;

class Ticker {
  constructor() {
    this.lastT = 0;
    this.f = bpm / 60;
  }
  start() {
    if (this.handle == undefined) {
      this.lastT = Date.now();
      this.handle = setInterval(() => {
        this.update();
      }, 10);
    }
  }
  update() {
    let t = Date.now() * 0.001;
    if (Math.floor(t * this.f) - Math.floor(this.lastT * this.f) > 0) {
      this.callback();
    }
    this.lastT = t;
  }
}

class Stick {
  constructor({ p, x, y }) {
    this.x = x;
    this.y = y;
    this.l = width / 2;
    this.lastR = 0;
    this.targetR = 0;
    this.r = 0;
    this.p = p;
    this.startT = 1000;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  line({ x1, y1, x2, y2, pg }) {
    pg.line(x1, y1, x2, y2);
  }
  draw({ t, pg }) {
    const p = this.p;
    const durT = 2;
    const tw = p.map(t - this.startT, 0, durT, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    this.line({ t, tw, x1: this.x1, y1: this.y1, x2: this.x2, y2: this.y2, pg });
    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
  }
}

class WaveStick extends Stick {
  constructor(args) {
    super(args)
  }
  line({ t, tw, x1, y1, x2, y2, pg }) {
    const p = this.p;
    const ease = EasingFunctions.easeInOutCubic(1 - Math.abs(1 - tw * 2));
    pg.push();
    const x0 = p.lerp(x1, x2, 0.5);
    const xh = p.mag(x2 - x1, y2 - y1) * 0.5;
    pg.translate(x0, p.lerp(y1, y2, 0.5));
    pg.rotate(Math.atan2(y2 - y1, x2 - x1));

    pg.noFill();
    pg.beginShape();
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = Math.sin(i * Math.PI) * ease;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * ease)) * height / 4);
    }
    pg.endShape();
    pg.pop();
  }
}

class CircleExpand {
  constructor({ t, p, x, y }) {
    this.startT = t;
    this.p = p;
    this.x = x;
    this.y = y;
  }
  draw({ t, pg }) {
    const p = this.p;
    const tw = t - this.startT;
    if (tw > 1) return;

    {
      const alpha = EasingFunctions.easeInOutCubic(1 - tw);
      pg.stroke(255, alpha * 255);
      pg.noFill();
    }
    {
      const ease = EasingFunctions.easeOutCubic(tw);
      pg.circle(this.x, this.y, ease * width / 4);
    }
  }
}

const s = (p) => {
  const synths = {};
  const ticker = new Ticker();
  const mainLayer = p.createGraphics(width, height);
  const drawers = [];
  const sticks = [];
  let isPlaying = false;
  let tickCount = 0;
  let perc;

  p.preload = () => { }

  p.setup = () => {
    p.createCanvas(width, height, p.WEBGL);
    p.frameRate(60);

    sticks.push(new Stick({ p, x: -width / 4, y: 0 }));
    sticks.push(new WaveStick({ p, x: width / 4, y: 0 }));

    ticker.callback = () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        if (tickCount % 4 == 0) {
        }
        if (tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1) {
          for (const stick of sticks) {
            stick.bang({ t });
          }
          setTimeout(() => {
            // synths.sine.triggerAttackRelease(p.random(["C4", "D4", "E4"]), "8n");
            synths.razor.triggerAttack(p.random([262, 294, 330]), 2);
          }, 250);
          setTimeout(() => {
            synths.razor.triggerRelease();
          }, 2000 + 250);
        }
        if (tickCount % 4 == 0 || tickCount == 4 * 3 - 1) {
          for (const stick of sticks) {
            drawers.push(new CircleExpand({ x: stick.x1, y: stick.y1, t, p }));
            drawers.push(new CircleExpand({ x: stick.x2, y: stick.y2, t, p }));
          }
          if (drawers.length > 10) drawers.shift();
          setTimeout(() => {
            perc.triggerAttackRelease();
          }, 250);
        }
        if (tickCount == 4 * 4 - 1) {
          // perc = p.random([synths.metal, synths.noise]);
        }
        tickCount = (tickCount + 1) % (4 * 4);
      }
    };
    synths.sine = new Tone.Synth({
      oscillator: { type: 'sine' }
    }).toMaster();
    synths.sine.volume.setValueAtTime(-20, 0);
    synths.am = new Tone.AMSynth().toMaster();
    synths.fm = new Tone.FMSynth().toMaster();
    synths.sawtooth = new Tone.Synth({
      oscillator: { type: 'sawtooth' }
    }).toMaster();
    synths.triangle = new Tone.Synth({
      oscillator: { type: 'triangle' }
    }).toMaster();
    synths.square = new Tone.Synth({
      oscillator: { type: 'square' }
    }).toMaster();
    synths.pluck = new Tone.PluckSynth().toMaster();
    synths.pluck.volume.setValueAtTime(-20, 0);
    synths.metal = new Tone.MetalSynth().toMaster();
    synths.metal.volume.setValueAtTime(-20, 0);
    synths.noise = new Tone.NoiseSynth().toMaster();
    synths.noise.volume.setValueAtTime(-20, 0);
    perc = synths.noise;
    synths.razor = new Razor();
  }

  p.mouseClicked = () => {
    runButtonClicked();
  }

  p.keyPressed = () => {
    // runButtonClicked();
  }

  p.draw = () => {
    let t = p.millis() * 0.001;
    ticker.update();

    if (isPlaying) { } else { }

    p.background(0);
    mainLayer.push();
    mainLayer.clear();
    mainLayer.translate(mainLayer.width / 2, mainLayer.height / 2);
    for (const stick of sticks) {
      stick.draw({ t, pg: mainLayer });
    }
    for (const drawer of drawers) {
      drawer.draw({ t, pg: mainLayer });
    }
    mainLayer.pop();
    p.imageMode(p.CENTER);
    p.image(mainLayer, 0, 0);
  }

  let isSetup = false;
  let runButtonClicked = () => {
    isPlaying = !isPlaying;
    if (isSetup == false) {
      ticker.start();
      isSetup = true;
    }
  }
}

class Razor {
  constructor() {
    this.isSetup = false;
    this.synths = [];
    this.numSynths = 32;
    for (let i = 0; i < this.numSynths; i++) {
      this.synths[i] = new Tone.Synth({
        oscillator: { type: 'sine' }
      }).toMaster();
      this.synths[i].volume.value = -40;
    }
  }
  // setup() {
  //   if (this.isSetup) return;
  //   this.isSetup = true;
  // }
  triggerAttack(freq, t) {
    for (let i = 0; i < this.synths.length; i++) {
      this.synths[i].triggerAttack(freq);
      this.synths[i].frequency.linearRampTo(freq * (i + 1), t)
    }
  }
  triggerRelease() {
    for (let i = 0; i < this.synths.length; i++) {
      this.synths[i].triggerRelease();
    }
  }
  connect() {
    //fakefakefake
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
{% endhighlight %}

<script>
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

// https://gist.github.com/gre/1650294/
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t * t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t * (2 - t) },
  // acceleration until halfway, then deceleration
  easeInOutQuad: function (t) { return t < .5 ? 2 * t * t : -1 + (4 - 2 * t) * t },
  // accelerating from zero velocity 
  easeInCubic: function (t) { return t * t * t },
  // decelerating to zero velocity 
  easeOutCubic: function (t) { return (--t) * t * t + 1 },
  // acceleration until halfway, then deceleration 
  easeInOutCubic: function (t) { return t < .5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1 },
  // accelerating from zero velocity 
  easeInQuart: function (t) { return t * t * t * t },
  // decelerating to zero velocity 
  easeOutQuart: function (t) { return 1 - (--t) * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuart: function (t) { return t < .5 ? 8 * t * t * t * t : 1 - 8 * (--t) * t * t * t },
  // accelerating from zero velocity
  easeInQuint: function (t) { return t * t * t * t * t },
  // decelerating to zero velocity
  easeOutQuint: function (t) { return 1 + (--t) * t * t * t * t },
  // acceleration until halfway, then deceleration
  easeInOutQuint: function (t) { return t < .5 ? 16 * t * t * t * t * t : 1 + 16 * (--t) * t * t * t * t },
}

const width = 400;
const height = 400;
const bpm = 80 * 4;

class Ticker {
  constructor() {
    this.lastT = 0;
    this.f = bpm / 60;
  }
  start() {
    if (this.handle == undefined) {
      this.lastT = Date.now();
      this.handle = setInterval(() => {
        this.update();
      }, 10);
    }
  }
  update() {
    let t = Date.now() * 0.001;
    if (Math.floor(t * this.f) - Math.floor(this.lastT * this.f) > 0) {
      this.callback();
    }
    this.lastT = t;
  }
}

class Stick {
  constructor({ p, x, y }) {
    this.x = x;
    this.y = y;
    this.l = width / 2;
    this.lastR = 0;
    this.targetR = 0;
    this.r = 0;
    this.p = p;
    this.startT = 1000;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  line({ x1, y1, x2, y2, pg }) {
    pg.line(x1, y1, x2, y2);
  }
  draw({ t, pg }) {
    const p = this.p;
    const durT = 2;
    const tw = p.map(t - this.startT, 0, durT, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    this.line({ t, tw, x1: this.x1, y1: this.y1, x2: this.x2, y2: this.y2, pg });
    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
  }
}

class WaveStick extends Stick {
  constructor(args) {
    super(args)
  }
  line({ t, tw, x1, y1, x2, y2, pg }) {
    const p = this.p;
    const ease = EasingFunctions.easeInOutCubic(1 - Math.abs(1 - tw * 2));
    pg.push();
    const x0 = p.lerp(x1, x2, 0.5);
    const xh = p.mag(x2 - x1, y2 - y1) * 0.5;
    pg.translate(x0, p.lerp(y1, y2, 0.5));
    pg.rotate(Math.atan2(y2 - y1, x2 - x1));

    pg.noFill();
    pg.beginShape();
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = Math.sin(i * Math.PI) * ease;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * ease)) * height / 4);
    }
    pg.endShape();
    pg.pop();
  }
}

class CircleExpand {
  constructor({ t, p, x, y }) {
    this.startT = t;
    this.p = p;
    this.x = x;
    this.y = y;
  }
  draw({ t, pg }) {
    const p = this.p;
    const tw = t - this.startT;
    if (tw > 1) return;

    {
      const alpha = EasingFunctions.easeInOutCubic(1 - tw);
      pg.stroke(255, alpha * 255);
      pg.noFill();
    }
    {
      const ease = EasingFunctions.easeOutCubic(tw);
      pg.circle(this.x, this.y, ease * width / 4);
    }
  }
}

const s = (p) => {
  const synths = {};
  const ticker = new Ticker();
  const mainLayer = p.createGraphics(width, height);
  const drawers = [];
  const sticks = [];
  let isPlaying = false;
  let tickCount = 0;
  let perc;

  p.preload = () => { }

  p.setup = () => {
    p.createCanvas(width, height, p.WEBGL);
    p.frameRate(60);

    sticks.push(new Stick({ p, x: -width / 4, y: 0 }));
    sticks.push(new WaveStick({ p, x: width / 4, y: 0 }));

    ticker.callback = () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        if (tickCount % 4 == 0) {
        }
        if (tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1) {
          for (const stick of sticks) {
            stick.bang({ t });
          }
          setTimeout(() => {
            // synths.sine.triggerAttackRelease(p.random(["C4", "D4", "E4"]), "8n");
            synths.razor.triggerAttack(p.random([262, 294, 330]), 2);
          }, 250);
          setTimeout(() => {
            synths.razor.triggerRelease();
          }, 2000 + 250);
        }
        if (tickCount % 4 == 0 || tickCount == 4 * 3 - 1) {
          for (const stick of sticks) {
            drawers.push(new CircleExpand({ x: stick.x1, y: stick.y1, t, p }));
            drawers.push(new CircleExpand({ x: stick.x2, y: stick.y2, t, p }));
          }
          if (drawers.length > 10) drawers.shift();
          setTimeout(() => {
            perc.triggerAttackRelease();
          }, 250);
        }
        if (tickCount == 4 * 4 - 1) {
          // perc = p.random([synths.metal, synths.noise]);
        }
        tickCount = (tickCount + 1) % (4 * 4);
      }
    };
    synths.sine = new Tone.Synth({
      oscillator: { type: 'sine' }
    }).toMaster();
    synths.sine.volume.setValueAtTime(-20, 0);
    synths.am = new Tone.AMSynth().toMaster();
    synths.fm = new Tone.FMSynth().toMaster();
    synths.sawtooth = new Tone.Synth({
      oscillator: { type: 'sawtooth' }
    }).toMaster();
    synths.triangle = new Tone.Synth({
      oscillator: { type: 'triangle' }
    }).toMaster();
    synths.square = new Tone.Synth({
      oscillator: { type: 'square' }
    }).toMaster();
    synths.pluck = new Tone.PluckSynth().toMaster();
    synths.pluck.volume.setValueAtTime(-20, 0);
    synths.metal = new Tone.MetalSynth().toMaster();
    synths.metal.volume.setValueAtTime(-20, 0);
    synths.noise = new Tone.NoiseSynth().toMaster();
    synths.noise.volume.setValueAtTime(-20, 0);
    perc = synths.noise;
    synths.razor = new Razor();
  }

  p.mouseClicked = () => {
    runButtonClicked();
  }

  p.keyPressed = () => {
    // runButtonClicked();
  }

  p.draw = () => {
    let t = p.millis() * 0.001;
    ticker.update();

    if (isPlaying) { } else { }

    p.background(0);
    mainLayer.push();
    mainLayer.clear();
    mainLayer.translate(mainLayer.width / 2, mainLayer.height / 2);
    for (const stick of sticks) {
      stick.draw({ t, pg: mainLayer });
    }
    for (const drawer of drawers) {
      drawer.draw({ t, pg: mainLayer });
    }
    mainLayer.pop();
    p.imageMode(p.CENTER);
    p.image(mainLayer, 0, 0);
  }

  let isSetup = false;
  let runButtonClicked = () => {
    isPlaying = !isPlaying;
    if (isSetup == false) {
      ticker.start();
      isSetup = true;
    }
  }
}

class Razor {
  constructor() {
    this.isSetup = false;
    this.synths = [];
    this.numSynths = 32;
    for (let i = 0; i < this.numSynths; i++) {
      this.synths[i] = new Tone.Synth({
        oscillator: { type: 'sine' }
      }).toMaster();
      this.synths[i].volume.value = -40;
    }
  }
  // setup() {
  //   if (this.isSetup) return;
  //   this.isSetup = true;
  // }
  triggerAttack(freq, t) {
    for (let i = 0; i < this.synths.length; i++) {
      this.synths[i].triggerAttack(freq);
      this.synths[i].frequency.linearRampTo(freq * (i + 1), t)
    }
  }
  triggerRelease() {
    for (let i = 0; i < this.synths.length; i++) {
      this.synths[i].triggerRelease();
    }
  }
  connect() {
    //fakefakefake
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>