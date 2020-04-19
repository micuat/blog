---
layout: post
title:  "Creative Code Berlin Online Jam May 2020"
author: naoto
categories: [ report ]
tags: [ essay ]
image: assets/images/2020-04-18-creative-code-berlin-200418.png
description: "a report"
featured: true
comments: true
p5: true
---

[Creative Code Berlin](https://twitter.com/CreativeCodeBLN) organized the second online coding jam on April 18, 2020. The report of the first online jam can be found [here]({{ site.baseurl }}/creative-code-berlin-200321).

![group photo in jitsi platform]({{ site.baseurl }}/assets/images/2020-04-18-creative-code-berlin-200418.png)

After the introduction round, we tried the idea of collaborative programming on p5.js. Last time we tried [OpenProcessing](https://www.openprocessing.org/), but this time we tried [repl.it](https://repl.it/), which is a platform for collaborative development. You can invite friends to your project and edit source files together similar to, for example, Google Docs. The difference to Google Docs is that you can run the sketch on the platform, which is ideal for prototyping. The downside is that if someone is working on a code that does not yet have a closing curly bracket, for example, then other people cannot run the code. Because of this reason, we need to explore further in the next jams to find out how to make collaborative coding accessible for beginners. Here is the sketch that Raphael, aBe and I made, based on the theme "egg, rain and gravity" :

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

At 4 pm we moved to a focus group about networked performances. I have been organizing a small group called Choreographic Coding Online every Saturday to open a space for everyone to experiment with networked performances. Because of the nature of networked performance, experimentation is easier when people with a similar interest gather at the same time. This time, I organized it as a subgroup of coding jam to invite from the jam. Jorge has been testing VDMX filters and sending the feed to virtual camera. I used [Hydra](https://hydra-editor.glitch.me/)'s new feature to add custom GLSL functions for chroma key and mixed the feed using OBS Studio.

![Jorge and I mixing videos]({{ site.baseurl }}/assets/images/2020-04-18-creative-code-berlin-200418-hydra.png)

This was another successful online coding jam, and again I would like to thank Raphael, Taru and aBe for organizing the event. It is remarkable that we made some improvements from the last jam based on the feedback we came up after the event. They are organizing a jam every month as well as "Stammtisch" (show & tell) and both are temporarily held online, so if you are interested please check out [Creative Code Berlin meetup page](https://www.meetup.com/creativeCodeBerlin/)!

<script>
// Topics: egg, rain, gravity

// never forget this dot!
// https://www.youtube.com/watch?v=M5d7vygUPoQ

// the vertex shader is called for each vertex
let vs = `
  precision highp float; 
  varying vec2 vPos;
  attribute vec3 aPosition;
  void main() { 
    vPos = (gl_Position = vec4(aPosition,1.0)).xy; 
  }`;

// the fragment shader is called for each pixel
let fs = `
  precision highp float; 
  uniform float time;
  varying vec2 vPos;
  void main() {
    gl_FragColor = vec4(0.0, sin(gl_FragCoord.y*0.1 + time)*0.5+0.5, 1.0, 1.0);
  }`;

let abeLayer;
//let fx;
function setup() {
	const rect = document.getElementById('p5sketch').getBoundingClientRect();
	createCanvas(rect.width, rect.width / 2).parent('p5sketch');
  // noCursor();
  naoto.setup();
  raph.setup();
  abeLayer = createGraphics(width, height);
  //fx = createShader(vs, fs);
  //shader(fx);
}

class Egg {
  setup() {
    this.eX = width / 2;
    this.eY = height;
    this.vX = random(-1, 1) * width / 4;
    this.vY = -width * random(0.8, 1.3);
    this.aX = 0;
    this.aY = width;
    this.cooked = false;
  }
  draw(t, pg) {
    const dt = 1 / 60;
    this.vX += dt * this.aX;
    this.eX += dt * this.vX;
    this.vY += dt * this.aY;
    this.eY += dt * this.vY;

    if(this.eY > height) {
      this.setup();
    }

    if(dist(this.eX, this.eY, mouseX, mouseY) < 50) {
      this.cooked = true;
    }

    pg.fill(255);
    pg.textAlign(CENTER, CENTER);
    let tween = sin(t);
    pg.textSize(60)// * abs(tween));
    if(this.cooked == false) {
      pg.text('🥚', this.eX, this.eY);
    }
    else {
      pg.text('🍳', this.eX, this.eY);
    }
    
  }
}
class Naoto {
  setup() {
    this.pg = createGraphics(width, height);
    this.eggs = [];
    for(let i = 0; i < 5; i++) {
      this.eggs.push(new Egg());
    }
    for(const egg of this.eggs) {
      egg.setup();
    }

  }
  draw() {
    const t = millis() * 0.001;
    let pg = this.pg;
    pg.clear();
    pg.fill(0, 0, 255, 80);
    // pg.fill(0, 0, 255, 80 * sin(t*PI/2) + 80);

    pg.noStroke();
    pg.noErase();

    let N = 10;
    for(let i = 0; i < N; i++) {
      const h = map(((noise(i/4, t) * 2 + t * 2) % 1), 0, 1, -height, height);
      pg.rect((i+.5) / N * width, h, .125 / N * width, height / 4);
    }

    pg.textSize(60);
    for(let sw of [true, false]) {
      for(let i = 0; i < N; i++) {
        let x = (i+.5) / N * width;
        let y = height - (sin((t + i) * 0.25 * Math.PI) * 50 + 50);

        if (sw) {
          pg.erase();
          pg.ellipse(x, y, width/N*2, width/N*2);
          pg.ellipse(x, y + 50, width/N*2, width/N*2);
        }
        else {
          pg.noErase();
          pg.fill(255);
          pg.text('☔', x, y);
        }
      }
    }
    
    pg.drawingContext.save();
    pg.drawingContext.shadowOffsetX = 0;
    pg.drawingContext.shadowOffsetY = 0;
    pg.drawingContext.shadowBlur = 20;
    // pg.drawingContext.shadowColor = 'rgba(255, 255, 255, 1)';
    // pg.erase();
    // pg.fill(255);
    // pg.ellipse(this.eX, this.eY, width/4, width/4);

    pg.drawingContext.shadowColor = 'rgba(0, 0, 0, .5)';
    pg.drawingContext.shadowOffsetX = 20;
    pg.drawingContext.shadowOffsetY = 20;

    pg.noErase();
    pg.text('🔥', mouseX, mouseY);

    for(const egg of this.eggs) {
      egg.draw(t, pg);
    }

    pg.drawingContext.restore();

    image(pg, 0, 0);
  }
}
let naoto = new Naoto();


class Raph {

  constructor() {
    this.radius = 10;
    this.mult = 0;
  }
  
  setup() {
    this.pg = createGraphics(windowWidth, windowHeight);
    this.pg.background(0);
    this.radius = this.pg.width * 0.1
    this.angleIterator = TWO_PI /45;
    this.lastPos = new createVector(0,0);
    this.currPos = new createVector(0,0);
  }

  draw() {

    push();
    translate(this.pg.width * 0.5, this.pg.height* 0.5);
    
    this.lastPos.x = 1 * this.radius * this.eggsFactor;
    this.lastPos.y = 0;
    for (this.angle = this.angleIterator; this.angle < TWO_PI; this.angle +=  this.angleIterator) {
      this.eggsFactor = map(sin(this.angle),-1.0,1.0,0.8,2.5);
      this.currPos.x = cos(this.angle) * this.radius * this.eggsFactor;
      this.currPos.y = sin(this.angle) * this.radius * this.eggsFactor;
      line(this.lastPos.x, this.lastPos.y,this.currPos.x, this.currPos.y)
      ellipse(this.currPos.x, this.currPos.y,10,10);
      this.lastPos = this.currPos;
    }
    pop();
  }
}
let raph = new Raph();


function draw() {
  // translate(-width/2, -height/2);
  background('orange');
  // so we are here together now? :)
  // yay!
  // nice, so we could have a function each? :)
  //fx.setUniform("time", millis() * 0.001);
  abe();
  //taru();
  naoto.draw(); // haha you said we were organized... classes!
  raph.draw();
}

// Info: there's no auto-run. Use CTRL+ENTER (or COMMAND+ENTER on a Mac)
function abe() {  
  randomSeed(frameCount / 100)
  var sz = frameCount % 100;
  abeLayer.stroke(255, 255, 255, 255 - sz * 2.5);
  abeLayer.noFill();
  abeLayer.clear()
  for(var i=0; i<20; i++) {;
    abeLayer.ellipse(random(width), random(height), sz, sz);
  }

  image(abeLayer, 0, 0);
  
}

function taru() {
  // left for lunch, byeee
  stroke('black');
  line(10, 50, width, 100);
}

// https://github.com/hamoid/Joukkue
// https://shdr.bkcore.com/
</script>
