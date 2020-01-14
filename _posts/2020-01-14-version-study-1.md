---
layout: post
title:  "Version Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2020-01-14-version-study-1.png
description: "A sketch"
featured: true
comments: true
p5: true
---

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>
<button onclick='activate(0)'>0</button>
<button onclick='activate(1)'>1</button>
<button onclick='activate(2)'>2</button>
<button onclick='activate(3)'>3</button>
<button onclick='activate(4)'>4</button>
<button onclick='activate(5)'>5</button>
<button onclick='activate(6)'>6</button>
<button onclick='activate(7)'>7</button>
<button onclick='activate(8)'>8</button>
<button onclick='activate(9)'>9</button>
<button onclick='activate(10)'>10</button>
<button onclick='activate(11)'>11</button>
<button onclick='activate(12)'>12</button>

<script>
var code = JSON.parse(`[
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n\\tbackground(255, 0, 255);\\n\\t\\n}\\n\\n draw=()=> {\\n\\tnoStroke();\\n\\tellipse(100,100,100);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194448"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n\\tbackground(255, 0, 255);\\n\\t\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85');\\n\\tnoStroke();\\n\\tellipse(100,100,100);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194623"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n\\tbackground(255, 0, 255);\\n\\t\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tellipse(100,100,100);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194635"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('60d394')\\n\\tellipse(100,100,100);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194702"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394')\\n\\tellipse(100,100,100);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194711"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\tellipse(width / 2,height / 2, r, r);\\n\\trect(100, 100, 200, 200);\\n}",
    "time": "20200114194751"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trect(0, 0, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114194904"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\trect(0, 0, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114194920"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\tlet t = millis() * 0.001;\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\tlet x = (Math.sin(t * Math.PI / 4) * 0.5 + 0.5) * r;\\n\\t\\trect(x, x, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114195006"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\tlet t = millis() * 0.001;\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\tlet x = (Math.sin((t + i) * Math.PI / 4) * 0.5 + 0.5) * r;\\n\\t\\trect(x, x, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114195024"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\tlet t = millis() * 0.001;\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\tlet x = (Math.sin((t + i * 0.25) * Math.PI / 4) * 0.5 + 0.5) * r;\\n\\t\\trect(x, x, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114195040"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\tlet t = millis() * 0.001;\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\tlet x = (Math.sin((t + i * 0.25) * Math.PI / 2) * 0.5 + 0.5) * r;\\n\\t\\trect(x, x, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114195052"
  },
  {
    "source": "setup=()=> {\\n\\tcreateCanvas(400, 400).parent('#p5sketch');\\n}\\n\\ndraw=()=> {\\n\\tlet t = millis() * 0.001;\\n\\t//https://coolors.co/ee6055-60d394-aaf683-ffd97d-ff9b85\\n\\tbackground('#ee6055');\\n\\tnoStroke();\\n\\tfill('#60d394');\\n\\tlet r = width / 4;\\n\\ttranslate(width / 2, height / 2);\\n\\tellipse(0, 0, r, r);\\n\\tfor(let i = 0; i < 4; i++) {\\n\\t\\tpush();\\n\\t\\trotate(i * Math.PI / 2);\\n\\t\\tlet x = (Math.sin(t * Math.PI / 2) * 0.5 + 0.5) * r;\\n\\t\\trect(x, x, r, r);\\n\\t\\tpop();\\n\\t}\\n}",
    "time": "20200114195111"
  }
]`)

function activate(n) {
  console.log('ha')
  let codeA = code[n].source
  eval(codeA)
  setup()
}
let codeA = code[0].source
eval(codeA)
</script>