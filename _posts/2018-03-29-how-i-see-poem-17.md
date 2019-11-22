---
layout: post
title:  "How I See Poem 17"
author: naoto
categories: [ sketch ]
tags: [ p5js, poem ]
image: assets/images/2018-03-29-How-I-See-Poem-17.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Text from Luving U by 6LACK.

An outcome from Machine Learning Literacy workshop led by Hannah Davis at the School for Poetic Computation.

<script>
// See you want me to be a pretty liar so bad
// But I can't fuck with karma cause she always comes back
// So call me every name you can find in the book
// But don't try to discredit every chance that I took

var text = [];
var angle0 = 0;
var s = function (sketch) {

  sketch.setup = function () {
    sketch.createCanvas(400, 400);
    text.push("See you want me\nto be a pretty liar\nso bad");
    text.push("But I can't fuck\nwith karma cause she\nalways comes back");
    text.push("So call me every name\nyou can find\nin the book");
    text.push("But don't try to\ndiscredit every chance\nthat I took");

    let myFont = sketch.loadFont('{{ site.baseurl }}/assets/fonts/FreeSans.ttf');
    sketch.textFont(myFont);
    sketch.textSize(32);

  }
  sketch.draw = function () {
    sketch.background(0);
    sketch.translate(sketch.width / 2, sketch.height / 2, -50);
    sketch.scale(0.5, 0.5);
    
    sketch.noStroke();
    sketch.fill(255, 100)
    sketch.ellipse(0, 0, 700*0.25);

    let t = sketch.millis()*0.0001;
    let index = sketch.floor(t*2) % text.length;

    sketch.fill(50)
    sketch.push();
    if((t+1) * 5 % 2 <= 1) {
      angle0 = (-sketch.cos(((t+1) * 5 % 1)*sketch.PI)+1)*0.5 * sketch.HALF_PI;
      angle0 += sketch.HALF_PI * (sketch.floor((t+1)*5) % 16) / 2
    }
    else {
      angle0 = sketch.HALF_PI * (0.5+(sketch.floor((t+1)*5) % 16) / 2);
    }
    sketch.rotate(-angle0);
    sketch.arc(0, 0, 700, 700, sketch.PI, sketch.PI * 3 / 2);

    sketch.fill(255);
    sketch.textAlign(sketch.CENTER);
    let tx = sketch.map(t % 1, 0, 1, -1, 1);
    tx *= sketch.width;
    sketch.rotate(-sketch.PI/4);
    sketch.text(text[index], 0, -270);//tx, -100)
    sketch.pop();

    for(let i = 0; i < 4; i++) {
      sketch.fill(255, (i + (t*10 % 1))*60);
      let r = 700 - 700/4*i - (t*10 % 1) * 700*0.25;
      sketch.arc(0, 0, r, r, 0, sketch.HALF_PI);
      sketch.arc(0, 0, r, r, sketch.PI, sketch.HALF_PI*3);
    }

    sketch.fill(255, 50, 50)
    sketch.push();
    if(t * 10 % 2 <= 1) {
      angle0 = (-sketch.cos((t * 10 % 1)*sketch.PI)+1)*0.5 * sketch.HALF_PI;
      angle0 += sketch.HALF_PI * (sketch.floor(t*10) % 16) / 2
    }
    else {
      angle0 = sketch.HALF_PI * (0.5+(sketch.floor(t*10) % 16) / 2);
    }
    sketch.rotate(angle0);
    sketch.arc(0, 0, 700, 700, 0, sketch.HALF_PI);
    sketch.pop();

    sketch.fill(255)
    sketch.push();
    if((t+0.25) * 20 % 2 <= 1) {
      angle0 = (-sketch.cos(((t+0.25) * 20 % 1)*sketch.PI)+1)*0.5 * sketch.HALF_PI;
      angle0 += sketch.HALF_PI * (sketch.floor((t+0.25)*20) % 16) / 2
    }
    else {
      angle0 = sketch.HALF_PI * (0.5+(sketch.floor((t+0.25)*20) % 16) / 2);
    }
    sketch.rotate(-angle0);
    sketch.arc(0, 0, 700*0.75, 700*0.75, sketch.PI, sketch.PI * 3 / 2);
    sketch.pop();

    sketch.fill(100)
    sketch.ellipse(0, 0, 40);
  }

};

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>