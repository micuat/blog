---
layout: post
title:  "Creative Code Berlin Online Jam"
author: naoto
categories: [ report ]
tags: [ essay ]
image: assets/images/2020-03-21-creative-code-berlin-200321.jpg
description: "a report"
featured: true
comments: true
p5: true
---

[Creative Code Berlin](https://twitter.com/CreativeCodeBLN) organized the first online coding jam on March 23, 2020. I have been to their "offline" coding jam before - usually the monthly jam is organized at co.up, a co-working space in Kreuzberg, Berlin, but due to the coronavirus situation, they organized an online version. there were around 10 participants, and we spent the whole afternoon to work on projects - either an entirely new project or a project that one has been working on - individually or as a group.

![group photo in jitsi platform]({{ site.baseurl }}/assets/images/2020-03-21-creative-code-berlin-200321.jpg)

We started with an introduction round followed by a brainstorming session. Usually we have several tables for brainstorming, but this time we used an online pad to write down and share ideas. One of the ideas was to experiment with collaborative online platforms, and we looked into a classroom feature of [OpenProcessing](https://www.openprocessing.org/). As one of us was cooking tacos, I made a [sketch](https://www.openprocessing.org/sketch/860320/) to draw tacos:

<div id = "p5sketch">
  <!-- p5 instance will be created here -->
</div>

Then I spent the afternoon to create a VJ framework - which I just started working on, and I hope it will be a long-term project that will be performed someday. You can see the sketch [here]({{ site.baseurl }}/2020-03-21-video-jockeying-2-ja) (the article is in Japanese, but there is a preview of the sketch).

There was also a sub-group discussing OpenRNDR, led by aBe and Kazik. OpenRNDR is a framework based on Kotlin for generative graphics and is gaining more and more attention from creative coders.

Besides the sub-group discussion, most of the time we kept quiet coding for hours (also, Raphael and Taru were using Rec Room to create installations in a shared VR space), which was an interesting experience as an online call always makes me feel that I need to talk. At the end of the jam, we shared what we did, and among them I really liked Rein's barcode oscillator:

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">I made a barcode synthesizer <a href="https://twitter.com/CreativeCodeBLN?ref_src=twsrc%5Etfw">@CreativeCodeBLN</a> <a href="https://t.co/TA02algRe5">pic.twitter.com/TA02algRe5</a></p>&mdash; Rein van der Woerd (@reinvanderwoerd) <a href="https://twitter.com/reinvanderwoerd/status/1241772279790473217?ref_src=twsrc%5Etfw">March 22, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 

It was such a nice jam not only to catch up with friends in Berlin from distance, but also to discover the potential of online collaboration. I thank the organizers Raphael, Taru and aBe for making this happen and all the participants for sharing such a nice time!

<script>
function setup() {
	createCanvas(400, 400).parent('p5sketch');
	background(100);
}

function draw() {
	textSize(mag(mouseX - pmouseX, mouseY - pmouseY));
	text("🌮", mouseX, mouseY);	
}
</script>
