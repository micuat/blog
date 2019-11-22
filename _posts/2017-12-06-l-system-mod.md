---
layout: post
title:  "L-System Mod"
author: naoto
categories: [ sketch ]
image: assets/images/2017-12-06-l-system-mod.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<script>
// Coding Rainbow
// Daniel Shiffman
// http://patreon.com/codingtrain
// Code for: https://youtu.be/E1B4UoSQMFw

// variables: A B
// axiom: A
// rules: (A → AB), (B → A)

var treeTexture;

var angle;
var axiom = "F";
var sentence = axiom;
var len = 100;

var rules = [];
rules[0] = {
  a: "F",
  b: "FLFL+[+FL-FLA-FL]-[-FL+FL+FL]"
}

function generate() {
  var nextSentence = "";
  for (var i = 0; i < sentence.length; i++) {
    var current = sentence.charAt(i);
    var found = false;
    for (var j = 0; j < rules.length; j++) {
      if (current == rules[j].a) {
        found = true;
        nextSentence += rules[j].b;
        break;
      }
    }
    if (!found) {
      nextSentence += current;
    }
  }
  if (nextSentence.length < 5000) {
    sentence = nextSentence;
    len *= 0.5;
    createP(sentence).parent("p5sketch");
    turtle();
  }

}

function draw() {
  turtle();
}
var count = 0;
function turtle() {
  count = 0;
  background(51);
  resetMatrix();
  translate(width / 2, height);
  stroke(255, 100);
  fill(255, 50, 20);
  for (var i = 0; i < sentence.length; i++) {
    count = i;
    var current = sentence.charAt(i);

    if (current == "F") {
      //image(treeTexture, 0, 0, 10, -len, 0, 0, 10, len);
      line(0, 0, 0, -len);
      translate(0, -len);
    } else if (current == "L") {
      drawLeaves(10);
    } else if (current == "A") {
      push();
  		var a = sin(millis() * 0.002 + count * 0.1);
		  rotate(a * PI / 6);
      translate(0, 5);
      ellipse(0, 0, 10, 10);
      pop();
    } else if (current == "+") {
      rotate(angle);
    } else if (current == "-") {
      rotate(-angle)
    } else if (current == "[") {
      push();
    } else if (current == "]") {
      pop();
    }
  }
}

function drawLeaves(s) {
  push();
  var a = sin(millis() * 0.004 + count * 0.1);
  rotate(a * PI / 6);
  drawLeaf(s);
  pop();
  push();
  scale(-1, 1);
  rotate(a * PI / 6);
  drawLeaf(s);
  pop();
}

function drawLeaf(s) {
  push();
  scale(s / 200, s / 200);
  translate(100, 0);
  fill(50, 150, 10);
  var a = -100,
    b = 0,
    c = -50,
    d = 50,
    e = 50,
    f = 50,
    g = 100,
    h = 0;
  stroke(0, 0, 0);
  bezier(a, b, c, d, e, f, g, h);
  bezier(a, b, c, -d, e, -f, g, h);
  pop();
}

function setup() {
  createCanvas(400, 400).parent("p5sketch");
  //treeTexture = createImg("https://slm-assets3.secondlife.com/assets/9454832/lightbox/bark__1__seamless__mp.jpg?1396417476", "treeTexture");
  var allImages = document.getElementsByTagName("img");
  var images = [];
  for (var i = 0, len = allImages.length; i < len; ++i) {
    if (allImages[i].alt == "treeTexture") {
      allImages[i].style.visibility = "hidden";
      allImages[i].style.height = "10px";
    }
  }
  angle = radians(25);
  background(51);
  createP(axiom).parent("p5sketch");
  turtle();
  var button = createButton("generate");
  button.mousePressed(generate).parent("p5sketch");
}
</script>