---
layout: post
title:  "Sound Texture Study 8"
author: naoto
categories: [ sketch ]
tags: [ p5js, sound ]
image: assets/images/2019-12-02-sound-texture-study-8.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Click to start. Snippet taken from [p-code](https://github.com/p-code-magazine/p-code).

<script>
var getFrequency = function(note) {
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
  return 440 * Math.pow(2, (keyNumber - 49) / 12);
};

var colorSchemes = [
  new ColorScheme("https://coolors.co/5386e4-7fc29b-b5ef8a-d7f171-817e9f"),
];

function ColorScheme(colorString) {
  this.colors = []; {
    let cc = colorString.split("/");
    let cs = cc[cc.length - 1].split("-");
    for (let i in cs) {
      let r = parseInt("0x" + cs[i].substring(0, 2));
      let g = parseInt("0x" + cs[i].substring(2, 4));
      let b = parseInt("0x" + cs[i].substring(4, 6));
      this.colors.push({
        r: r,
        g: g,
        b: b
      });
    }
  }
  this.offset = 0;
}

ColorScheme.prototype.get = function(i) {
  i = Math.min(this.colors.length - 1, Math.max(0, i));
  return this.colors[(i + this.offset) % this.colors.length];
}

function setColor(parent, func, index, alpha) {
  if (alpha == undefined) alpha = 255;
  parent[func](colorSchemes[0].get(index).r, colorSchemes[0].get(index).g, colorSchemes[0].get(index).b, alpha);
}

const s = (p) => {
  let sine;
  let saw;
  let tri;
  let square;
  let noise;

  let freq = 440;
  let pointer = 0;
  let codeInput;
  let runButton;
  let output;
  let tokens = [];

  let isPlaying = false;
  let prevChar = '';

  let codeBase = '<<<440<<+3200<~<<<<100^=';
  let pastCommands = [];
  let colorShift = 1;

  p.setup = () => {
    p.createCanvas(400, 400);
    p.frameRate(30);

    sine = new p5.Oscillator(freq, 'sine');
    saw = new p5.Oscillator(freq, 'sawtooth');
    tri = new p5.Oscillator(freq, 'triangle');
    square = new p5.Oscillator(freq, 'square');
    noise = new p5.Noise('white');

    codeInput = p.createInput(codeBase);
    codeInput.size(p.width);

    codeInput.elt.onkeyup = runButtonClicked;
  }
  
  p.mouseClicked = () => {
    runButtonClicked();
  }

  p.draw = () => {
    setColor(p, 'background', (colorShift + 3) % 5);
    p.noStroke();
    p.translate(p.width/2, 0);
    p.rectMode(p.CENTER);
    if (isPlaying) {
      if (pointer < tokens.length) {
        let node = tokens[pointer];
        execute(node);
        if (!isNaN(node)) {
          pastCommands.push(freq);
        }
        else {
          pastCommands.push(node);
        }
        if (pastCommands.length > 15) pastCommands.shift();
        pointer++;

        if (!isNaN(node) && node > 1000) {
          colorShift++;
          if (colorShift > 4) colorShift = 0;
        }
        p.textAlign(p.CENTER, p.CENTER);
        let h = p.width / 16;
        p.textSize(h);
        for(let i = 0; i < pastCommands.length; i++) {
          const index = (i-(p.frameCount%pastCommands.length)+pastCommands.length) % pastCommands.length;
          const command = pastCommands[index];

          if (!isNaN(command)) {
            if (index == pastCommands.length - 1)
              setColor(p, 'fill', (colorShift + 2) % 5);
            else
              setColor(p, 'fill', (colorShift + 0) % 5);
            p.rect(0, (i+1) * h, (command % 4000) / 4000 * p.width, h);
          }
          else if (command == '=') {
          }
          else {
            if (index == pastCommands.length - 1)
              setColor(p, 'fill', (colorShift + 2) % 5);
            else
              setColor(p, 'fill', (colorShift + 1) % 5);
            // p.text(command, 0, (i+1) * h);
            p.rect(0, (i+1) * h, (h), h);
          }
        }
      } else {
        isPlaying = false;
      }
    } else {
      sine.stop();
      saw.stop();
      tri.stop();
      square.stop();
      noise.stop();
      prevChar = '';
    }
  }

  let runButtonClicked = () => {
    isPlaying = true;

    let code = codeInput.value();
    let unbalancedBrackets = (code.split("<").length - 1) - (code.split(">").length - 1);
    if (unbalancedBrackets > 0) {
      code += '>'.repeat(unbalancedBrackets);
    }
    code = unpack(code);

    while (code.indexOf('<') > -1) {
      code = unpack(code);
    }

    let lex = code.match(/(\D+)|[+-]?(\d*[.])?\d+/gi);
    parse(lex);
  }

  let unpack = (code, index) => {
    let pointer = 0;
    let result = '';
    let start = 0;
    let end = 0;
    let stack = 0;

    let peek = () => {
      return code[pointer];
    }

    let consume = () => {
      pointer++;
    }

    while (pointer < code.length) {
      let t = peek();
      if (t === "<") {
        if (stack == 0) {
          start = pointer;
        }
        stack++;
      } else if (t === ">") {
        end = pointer;
        stack--;
        if (stack == 0) {
          result += code.slice(start + 1, end).repeat(2);
        }
      } else {
        if (stack == 0) {
          result += t;
        }
      }
      consume();
    }

    return result;
  }


  let parse = (l) => {
    pointer = 0;
    tokens = [];
    if (l) {
      for (let i = 0; i < l.length; i++) {
        if (isNaN(l[i])) {
          let chars = l[i].split('');
          for (let j = 0; j < chars.length; j++) {
            tokens.push(chars[j]);
          }
        } else {
          tokens.push(l[i]);
        }
      }
    }
  }

  let execute = (t) => {
    if (t != prevChar) {
      if (isNaN(t)) {
        switch (t) {
          case '~':
            sine.start();
          case 'N':
            saw.start();
            break;
          case '^':
            tri.start();
            break;
          case '[':
            square.start();
            break;
          case '=':
            sine.stop();
            saw.stop();
            tri.stop();
            square.stop();
            noise.stop();
            break;
          case '+':
          case '-':
          case '*':
          case '/':
          case '<':
          case '>':
            break;
          default:
            noise.start();
        }
      } else {
        if (prevChar == "+") {
          freq += parseFloat(t);
        } else if (prevChar == "-") {
          freq -= parseFloat(t);
        } else if (prevChar == "*") {
          freq *= parseFloat(t);
        } else if (prevChar == "/") {
          freq /= parseFloat(t);
        } else {
          freq = parseFloat(t);
        }

        sine.freq(freq);
        saw.freq(freq);
        tri.freq(freq);
        square.freq(freq);
      }
    }
    prevChar = t;
  }
}

let myp5 = new p5(s, document.getElementById('p5sketch'));
</script>