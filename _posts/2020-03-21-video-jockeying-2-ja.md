---
layout: post
title:  "VJ について 2"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound, essay, japanese ]
image: assets/images/2020-03-21-video-jockeying-2-ja.png
description: "スケッチ"
featured: true
comments: true
p5: true
tonejs: true
---

[前回]({{ site.baseurl }}/video-jockeying-1-ja)を踏まえて一から簡単なスケッチを書いてみました。クリックをすると再生と一時停止を切り替えられます。

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

新しい VJ 用のフレームワークを作ろうと思っていたので、[クリエイティブ・コード・ベルリン](https://www.meetup.com/creativeCodeBerlin/)のオンライン・ジャム（通常はコワーキングスペースに集まって個人またはグループでコーディングをする会なのですが、今回はそのオンライン版）に参加したことをきっかけに一から p5.js と Tone.js でコードを書いてみました。

ホワイト・ノイズをパーカッションとして鳴らしながら円を描くだけのシンプルなスケッチですが、コードは今後の展開を想定して少し複雑にしています。まず、Ticker クラスが10ミリ秒ごとにループして BPM にあわせてコールバックを呼びます（この部分は Tone.js 等を用いて効率化できると思います）。円は CircleExpand クラスが描画していて、中心は固定の位置、半径と線のアルファは経過した時間に応じて変化します。このクラスは今後 Stick や他の図形から直接呼ばれるようにした方が扱いやすいかなと考えています。

音は描画よりも少し遅らせた方が知覚的にバランスが取れると思っているので遅延させています。また、次回以降に音色を増やした際に円以外の図形が出るように、そしてその図形や色と音の質が近く感じられるような表現を目指したいと思います。

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
    this.startT = 0;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  draw({ t, pg }) {
    const p = this.p;
    const durT = 2;
    const tw = p.map(t - this.startT, durT * 2 / 8, durT * 4 / 8, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    pg.line(this.x1, this.y1, this.x2, this.y2);
    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
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
    sticks.push(new Stick({ p, x: width / 4, y: 0 }));

    ticker.callback = () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        if (tickCount % 4 == 0) {
        }
        if (tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1) {
          for (const stick of sticks) {
            stick.bang({ t });
          }
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
    this.startT = 0;
  }
  bang({ t }) {
    this.lastR = this.targetR;
    // this.targetR = Math.floor(Math.random() * 2) * Math.PI;
    this.startT = t;
  }
  draw({ t, pg }) {
    const p = this.p;
    const durT = 2;
    const tw = p.map(t - this.startT, durT * 2 / 8, durT * 4 / 8, 0, 1, true);
    const ease = EasingFunctions.easeInOutCubic(tw);
    this.r = p.lerp(this.lastR, this.targetR, ease);
    this.x1 = this.x + this.l * 0.5 * Math.cos(this.r);
    this.y1 = this.y + this.l * 0.5 * Math.sin(this.r);
    this.x2 = this.x + this.l * 0.5 * Math.cos(this.r + Math.PI);
    this.y2 = this.y + this.l * 0.5 * Math.sin(this.r + Math.PI);
    pg.stroke(255);
    pg.line(this.x1, this.y1, this.x2, this.y2);
    pg.noStroke();
    pg.fill(255);
    pg.circle(this.x1, this.y1, width / 40);
    pg.circle(this.x2, this.y2, width / 40);
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
    sticks.push(new Stick({ p, x: width / 4, y: 0 }));

    ticker.callback = () => {
      if (isPlaying) {
        t = p.millis() * 0.001;
        if (tickCount % 4 == 0) {
        }
        if (tickCount % 4 == 2 && Math.floor(tickCount / 4) < 1) {
          for (const stick of sticks) {
            stick.bang({ t });
          }
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

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>