---
layout: post
title:  "Processing Community Hangout Japan 3"
author: naoto
categories: [ report ]
tags: [ essay ]
image: assets/images/2020-08-30-processing-community-hangout-japan-3.png
description: "a report"
featured: true
comments: true
---

On August 30th, 2020, the Processing Community Japan organized a Processing Community Hangout. We have been organizing an online event for Japanese Processing and p5.js users, and this is the third edition. The event was organized and facilitated by [Takawo-san](https://twitter.com/takawo/) and me, with guests, [Nagayama-san](https://twitter.com/nagayama) as a commentator and [Ayato](https://twitter.com/dn0t_), who is announcing an open call for the second collective Japanese Processing Zine.

<div class="youtube-container">
<iframe class="youtube-video" width="560" height="315" src="https://www.youtube.com/embed/aM7-Bu0LeiI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

This edition featured [#つぶやきProcessing](https://twitter.com/search?q=%23%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8Dprocessing&src=typed_query), which literally translates to "Tweet Processing". It is a format proposed by [Hau-kun](https://twitter.com/Hau_kun), a challenge to fit a Processing code in one tweet. After recent change in Twitter, one tweet can contain up to 280 characters; however, since "2-byte" characters are counted as 2 characters, the hashtag #つぶやきProcessing takes 19 characters.

In this streaming, we asked Hau-kun from Japan and [Alexandre Villares](https://twitter.com/villares) from Brazil to record 30-minute coding videos. Alexandre is a technology and art educator in Sao Paulo, who organizes [Noite de Processing](https://garoa.net.br/wiki/Noite_de_Processing) (Night of Processing), a meetup currently run online, and they invited Japanese Processing coders to their event when they featured つぶやきProcessing in June. Therefore, I asked Alexandre for the Japanese Hangout to continue an exchange.

Hau-kun's video starts around [2:30](https://youtu.be/aM7-Bu0LeiI?t=150). They recorded the screen as well as their voiceover (in Japanese) which is very informative. They code in the original Java mode of Processing. Their strategy is to first make a draft code and then to minimize the code to fit into a tweet. They took inspiration from bubbles and made circular pattern as seen in the following Twitter embed. Discovering which parameter affects which part of the rendered image is crucial in their process, and while cutting some characters of the code, they encounter new images by accident. They often use `mouseX` input to tune parameters, and it is impressive to see them using till the last second to decide parameters.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">float t,r,S;<br>void setup(){size(720,720);colorMode(HSB);}<br>void draw(){<br>fill(0,8);<br>square(0,0,721);<br>noFill();<br>t+=.05;<br>for(r=0;r&lt;TAU*9;r+=.1){<br>stroke((r*r)%255,255,255,64/(S=tan(.5*t+r*.3)));<br>circle(cos(r+t)*r*(9+S)+360,sin(r+t)*r*(9-S)+360,20*S);<br>}<br>}<a href="https://twitter.com/hashtag/pchj03?src=hash&amp;ref_src=twsrc%5Etfw">#pchj03</a> <a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8DProcessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきProcessing</a> <a href="https://t.co/h7on7uolR8">pic.twitter.com/h7on7uolR8</a></p>&mdash; はぅ君 (@Hau_kun) <a href="https://twitter.com/Hau_kun/status/1300026844654178304?ref_src=twsrc%5Etfw">August 30, 2020</a></blockquote>

Alexandre's video can be found around [42:06](https://youtu.be/aM7-Bu0LeiI?t=2526) and you can follow it as they talk in English (and while watching, we made voiceover in Japanese). Their coding environment is [Processing.py](https://py.processing.org/), which is a python-binding of Processing. There are a number of tweet Processing coders who use Processing.py as it has some advantages (and disadvantages) compared to Processing and p5.js. Alexandre's inspiration was a flower-seed pattern from [Numberphile](https://www.youtube.com/user/numberphile), which coincidentally turned out to take a similar direction to Hau-kun's image.

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">s=sqrt(5)/2+.5#つぶやきProcessing<br>w,h=400,200<br>def setup():size(w,w); noStroke();colorMode(3)<br>def draw():<br> clear() <br> r = 1<br> for i in range(3000):<br> fill(int(i*.1+frameCount)%256,255,255,100)<br> a=i*s<br> x=h+r*cos(a)<br> y=h+r*sin(a)<br> square(x,y,r/10)<br> r+=0.1 <a href="https://t.co/1LI4PDT73i">pic.twitter.com/1LI4PDT73i</a></p>&mdash; Alexandre B A Villares☂ (@villares) <a href="https://twitter.com/villares/status/1299474669146451968?ref_src=twsrc%5Etfw">August 28, 2020</a></blockquote>

After watching the videos, Takawo-san, Nagayama-san and I looked into #つぶやきProcessing contributed by other community members to decipher them.

![submissions to neort]({{ site.baseurl }}/assets/images/2020-08-30-processing-community-hangout-japan-3-neort.jpg)

You can find the submitted sketches on [NEORT](https://neort.io/tag/bsnu7nk3p9f416a8lo90). We are glad that some of the community members tried Tweet Processing for the first time for this opportunity. What was more interesting that some experienced users helped new people to shorten their codes to fit into a single tweet. I would like to close the writeup with some of those first tweet-processing codes. I would like to thank Hau-kun and Alexandre for kindly sending us videos and everyone who submitted sketches and participated in the event.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">👻<br>y=0,f=0;setup=()=&gt;{createCanvas(w=600,w),translate(w/2,w/2),background(0);stroke(&quot;<a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8DProcessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきProcessing</a>&quot;);e=w/5,v=e/2;c=circle,c(v,0,e),<br>beginShape(),m=vertex,m(e,0),m(0,0),m(0,e);<br>for(t=0;t&lt;e;t+=.5)y=e+9*sin(radians(f)),f+=8,m(t,y);endShape(),fill(0),c(v,0,9),c(e/4,0,9)} <a href="https://t.co/Htc9v24qnd">pic.twitter.com/Htc9v24qnd</a></p>&mdash; センバク (@senbaku) <a href="https://twitter.com/senbaku/status/1299724115784265728?ref_src=twsrc%5Etfw">August 29, 2020</a></blockquote>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">setup=()=&gt;{<br>createCanvas(W=720, W)<br>rectMode(CENTER)<br>r=random<br>for(i=0;i&lt;500;i++){<br>ps=r(60)<br>cr=ps/4<br>co=color(255, r(120, 250), r(50, 300))<br>if(r()&lt;0.7){fill(co)<br>noStroke()}else{stroke(co)<br>noFill()}square(r(W),r(W),ps,r([0,cr]),r([0,cr]),r([0,cr]),r([0,cr]))}}//<a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8Dprocessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきprocessing</a> <a href="https://t.co/DTAXIcXMCx">pic.twitter.com/DTAXIcXMCx</a></p>&mdash; 斎藤 saito (@coding_ape) <a href="https://twitter.com/coding_ape/status/1298507595829788673?ref_src=twsrc%5Etfw">August 26, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 
