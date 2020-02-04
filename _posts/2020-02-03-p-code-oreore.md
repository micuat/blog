---
layout: post
title:  "p-code oreore"
author: naoto
categories: [ platform ]
tags: [ p5js, sound ]
image: assets/images/2020-02-03-p-code-oreore.png
description: "p-code oreore platform"
featured: true
comments: true
---

[open](https://p-code-oreore.glitch.me/)

p-code oreore is an experimental chat platform for collective music live-coding. Syntax is based on [p-code](https://github.com/p-code-magazine/p-code) but numbers denote MIDI notes not frequencies, and some features are added.


Acknowledgements
--------

This project is a shameless clone of [p-code playground](https://github.com/h4us/p-code-playground) developed by Yosuke Hayashi and Haus.

The chat platform is run by [glitch.com](https://glitch.com/) and is inspired by [pixeljam](http://pixeljam.glitch.me/) by Olivia Jack.


Syntax
--------

| notation | description |
| ------------- | ------------- |
| `~` | sine wave |
| `^` | triangle wave |
| `N` | sawtooth wave |
| `[` | square wave |
| `a` | AM synth |
| `f` | FM synth |
| `m` | metal synth |
| `p` | pluck synth |
| `=` | mute |
| `<CODE>` | repeat `CODE` surrounded by `<>` |
| `dCODEb` | add feedback loop filter to `CODE` |
| `NUMBER` | set MIDI note |
| `+NUMBER` | add to current note |
| `-NUMBER` | subtract from current note |
| `*NUMBER` | multiply current node |
| `/NUMBER` | divide current note |

`NUMBER` bigger than `120` will be replaced by a random frequency. Any unrecognized character will play white noise. If `<` is not closed, `>` will be automatically added at the end.

Examples
--------

    10<<<<<<<<<<<<<<<+1~

sweep from 10 and play as sine wave (eventually become random tones as it exceeds 120).

