---
layout: post
title:  "VJ について 4"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound, essay, japanese ]
image: assets/images/2020-03-23-video-jockeying-4-ja.png
description: "スケッチ"
featured: true
comments: true
p5: true
tonejs: true
---

クリックをすると再生と一時停止を切り替えられます。

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

描画にほとんど変更点はありませんが、音のイメージに合わせてソリッドな線が点になるようにしました。その際になるべくコードがきれいになるように、イベント・ドリブンを意識して WaveStick クラスが WaveStickDots を呼ぶようにしています。それにあわせて全ての描画クラスを DrawerManager で管理するようにしました。まだ設計については実験中で、今後昨日を追加しながら変更を加えていく予定です。

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
    this.tickCount = 0;
    this.tickAmount = 4 * 4;
    this.callbacks = [];
  }
  on(condition, callback) {
    this.callbacks.push({ condition, callback });
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
      // this.callback({ tickCount: this.tickCount });
      for (const pair of this.callbacks) {
        if (pair.condition({ tickCount: this.tickCount })) {
          pair.callback();
        }
      }
      this.tickCount = (this.tickCount + 1) % this.tickAmount;
    }
    this.lastT = t;
  }
}

class Stick {
  constructor({ p, x, y, pg, startT }) {
    this.x = x;
    this.y = y;
    this.l = width / 2;
    this.lastR = 0;
    this.targetR = 0;
    this.r = 0;
    this.p = p;
    this.pg = pg;
    this.startT = startT == undefined ? 1000 : startT;
    this.isDead = false;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  edgeBang({ t }) {
    const p = this.p;
    const pg = this.pg;
    p.drawers.add(new CircleExpand({ x: this.x1, y: this.y1, t, p, pg }));
    p.drawers.add(new CircleExpand({ x: this.x2, y: this.y2, t, p, pg }));
  }
  line({ xh, pg }) {
    pg.line(-xh, 0, xh, 0);
  }
  draw({ t }) {
    const p = this.p;
    const pg = this.pg;
    const durT = 2.5;
    const tw = p.map(t - this.startT, 0, durT, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    pg.push();

    const x0 = p.lerp(this.x1, this.x2, 0.5);
    const xh = p.mag(this.x2 - this.x1, this.y2 - this.y1) * 0.5;
    pg.translate(x0, p.lerp(this.y1, this.y2, 0.5));
    pg.rotate(Math.atan2(this.y2 - this.y1, this.x2 - this.x1));
    this.line({ t, tw, xh, pg });
    pg.pop();

    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
  }
}

class WaveStick extends Stick {
  constructor(args) {
    super(args);
    this.spawned = false;
  }
  line({ t, tw, xh }) {
    const p = this.p;
    const pg = this.pg;

    if (tw >= 0.5 && this.spawned == false) {
      p.drawers.add(new WaveStickDots({ p, x: this.x, y: this.y, pg, startT: this.startT }));
      this.spawned = true;
    }
    if (tw < 0.5) {
      this.spawned = false;
    }

    pg.noFill();

    const ease = EasingFunctions.easeInOutCubic(1 - Math.abs(1 - tw * 2));
    pg.stroke(255, 255 * 1);
    pg.beginShape();
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = (1 - Math.cos(i * Math.PI * 2)) * ease / 2;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * ease)) * height / 4);
    }
    pg.endShape();
  }
}

class WaveStickDots extends Stick {
  constructor(args) {
    super(args);
  }
  line({ t, tw, xh }) {
    const p = this.p;
    const pg = this.pg;
    if (tw >= 1) {
      this.isDead = true;
      return;
    }

    pg.noFill();

    const alpha = EasingFunctions.easeInOutCubic(p.map(tw, 0.9, 1, 1, 0, true));
    pg.stroke(255, 255 * alpha);
    pg.beginShape(p.POINTS);
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = (1 - Math.cos(i * Math.PI * 2)) / 2;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * 1)) * height / 4);
    }
    pg.endShape();
  }
}

class CircleExpand {
  constructor({ t, p, x, y, pg }) {
    this.startT = t;
    this.p = p;
    this.x = x;
    this.y = y;
    this.pg = pg;
    this.isDead = false;
  }
  draw({ t }) {
    const p = this.p;
    const pg = this.pg;
    const tw = t - this.startT;
    if (tw > 1) {
      this.isDead = true;
      return;
    }
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

class DrawerManager {
  constructor() {
    this.drawers = [];
  }
  add(a) {
    this.drawers.push(a);
  }
  update({ t }) {
    for (const drawer of this.drawers) {
      drawer.draw({ t });
    }
    for (let i = this.drawers.length - 1; i >= 0; i--) {
      if (this.drawers[i].isDead) {
        this.drawers.splice(i, 1);
      }
    }
  }
}

const s = (p) => {
  const synths = {};
  const ticker = new Ticker();
  const mainLayer = p.createGraphics(width, height);
  p.drawers = new DrawerManager();
  const sticks = [];
  let isPlaying = false;
  let perc;

  p.preload = () => { }

  p.setup = () => {
    p.createCanvas(width, height, p.WEBGL);
    p.frameRate(60);

    sticks.push(new Stick({ p, x: -width / 4, y: 0, pg: mainLayer }));
    sticks.push(new WaveStick({ p, x: width / 4, y: 0, pg: mainLayer }));
    for (const stick of sticks) {
      p.drawers.add(stick);
    }

    ticker.on(({ tickCount }) => {
      return tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1
    }, () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        for (const stick of sticks) {
          stick.bang({ t });
        }
        setTimeout(() => {
          synths.razor.triggerAttack(p.random([262, 294, 330]), 2);
        }, 250);
        setTimeout(() => {
          synths.razor.triggerRelease();
        }, 2000 + 250);
      }
    });

    ticker.on(({ tickCount }) => {
      return tickCount % 4 == 0 || tickCount == 4 * 3 - 1
    }, () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        for (const stick of sticks) {
          stick.edgeBang({ t });
        }
        setTimeout(() => {
          perc.triggerAttackRelease();
        }, 250);
      }
    });

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

    p.background(0);
    mainLayer.push();
    mainLayer.clear();
    mainLayer.translate(mainLayer.width / 2, mainLayer.height / 2);

    p.drawers.update({ t });

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
    this.tickCount = 0;
    this.tickAmount = 4 * 4;
    this.callbacks = [];
  }
  on(condition, callback) {
    this.callbacks.push({ condition, callback });
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
      // this.callback({ tickCount: this.tickCount });
      for (const pair of this.callbacks) {
        if (pair.condition({ tickCount: this.tickCount })) {
          pair.callback();
        }
      }
      this.tickCount = (this.tickCount + 1) % this.tickAmount;
    }
    this.lastT = t;
  }
}

class Stick {
  constructor({ p, x, y, pg, startT }) {
    this.x = x;
    this.y = y;
    this.l = width / 2;
    this.lastR = 0;
    this.targetR = 0;
    this.r = 0;
    this.p = p;
    this.pg = pg;
    this.startT = startT == undefined ? 1000 : startT;
    this.isDead = false;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  edgeBang({ t }) {
    const p = this.p;
    const pg = this.pg;
    p.drawers.add(new CircleExpand({ x: this.x1, y: this.y1, t, p, pg }));
    p.drawers.add(new CircleExpand({ x: this.x2, y: this.y2, t, p, pg }));
  }
  line({ xh, pg }) {
    pg.line(-xh, 0, xh, 0);
  }
  draw({ t }) {
    const p = this.p;
    const pg = this.pg;
    const durT = 2.5;
    const tw = p.map(t - this.startT, 0, durT, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    pg.push();

    const x0 = p.lerp(this.x1, this.x2, 0.5);
    const xh = p.mag(this.x2 - this.x1, this.y2 - this.y1) * 0.5;
    pg.translate(x0, p.lerp(this.y1, this.y2, 0.5));
    pg.rotate(Math.atan2(this.y2 - this.y1, this.x2 - this.x1));
    this.line({ t, tw, xh, pg });
    pg.pop();

    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
  }
}

class WaveStick extends Stick {
  constructor(args) {
    super(args);
    this.spawned = false;
  }
  line({ t, tw, xh }) {
    const p = this.p;
    const pg = this.pg;

    if (tw >= 0.5 && this.spawned == false) {
      p.drawers.add(new WaveStickDots({ p, x: this.x, y: this.y, pg, startT: this.startT }));
      this.spawned = true;
    }
    if (tw < 0.5) {
      this.spawned = false;
    }

    pg.noFill();

    const ease = EasingFunctions.easeInOutCubic(1 - Math.abs(1 - tw * 2));
    pg.stroke(255, 255 * 1);
    pg.beginShape();
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = (1 - Math.cos(i * Math.PI * 2)) * ease / 2;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * ease)) * height / 4);
    }
    pg.endShape();
  }
}

class WaveStickDots extends Stick {
  constructor(args) {
    super(args);
  }
  line({ t, tw, xh }) {
    const p = this.p;
    const pg = this.pg;
    if (tw >= 1) {
      this.isDead = true;
      return;
    }

    pg.noFill();

    const alpha = EasingFunctions.easeInOutCubic(p.map(tw, 0.9, 1, 1, 0, true));
    pg.stroke(255, 255 * alpha);
    pg.beginShape(p.POINTS);
    for (let i = 0; i <= 1; i += 0.01) {
      const x = p.lerp(-xh, xh, i);
      const env = (1 - Math.cos(i * Math.PI * 2)) / 2;
      pg.vertex(x, env * Math.sin(Math.PI * (t * 8 + i * 4 * 1)) * height / 4);
    }
    pg.endShape();
  }
}

class CircleExpand {
  constructor({ t, p, x, y, pg }) {
    this.startT = t;
    this.p = p;
    this.x = x;
    this.y = y;
    this.pg = pg;
    this.isDead = false;
  }
  draw({ t }) {
    const p = this.p;
    const pg = this.pg;
    const tw = t - this.startT;
    if (tw > 1) {
      this.isDead = true;
      return;
    }
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

class DrawerManager {
  constructor() {
    this.drawers = [];
  }
  add(a) {
    this.drawers.push(a);
  }
  update({ t }) {
    for (const drawer of this.drawers) {
      drawer.draw({ t });
    }
    for (let i = this.drawers.length - 1; i >= 0; i--) {
      if (this.drawers[i].isDead) {
        this.drawers.splice(i, 1);
      }
    }
  }
}

const s = (p) => {
  const synths = {};
  const ticker = new Ticker();
  const mainLayer = p.createGraphics(width, height);
  p.drawers = new DrawerManager();
  const sticks = [];
  let isPlaying = false;
  let perc;

  p.preload = () => { }

  p.setup = () => {
    p.createCanvas(width, height, p.WEBGL);
    p.frameRate(60);

    sticks.push(new Stick({ p, x: -width / 4, y: 0, pg: mainLayer }));
    sticks.push(new WaveStick({ p, x: width / 4, y: 0, pg: mainLayer }));
    for (const stick of sticks) {
      p.drawers.add(stick);
    }

    ticker.on(({ tickCount }) => {
      return tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1
    }, () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        for (const stick of sticks) {
          stick.bang({ t });
        }
        setTimeout(() => {
          synths.razor.triggerAttack(p.random([262, 294, 330]), 2);
        }, 250);
        setTimeout(() => {
          synths.razor.triggerRelease();
        }, 2000 + 250);
      }
    });

    ticker.on(({ tickCount }) => {
      return tickCount % 4 == 0 || tickCount == 4 * 3 - 1
    }, () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        for (const stick of sticks) {
          stick.edgeBang({ t });
        }
        setTimeout(() => {
          perc.triggerAttackRelease();
        }, 250);
      }
    });

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

    p.background(0);
    mainLayer.push();
    mainLayer.clear();
    mainLayer.translate(mainLayer.width / 2, mainLayer.height / 2);

    p.drawers.update({ t });

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