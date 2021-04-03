---
layout: post
title:  "For code that breaks on the stage"
author: naoto
categories: [ report ]
tags: [ essay ]
image: assets/images/2021-03-21-producing-with-code.jpg
description: "tips"
featured: true
comments: true
---

Coding sketches to make visuals and sounds or hacking hardware with code is fun, but as soon as you use it to perform or exhibit it, things do not work as you expected ([Murphy's law](https://en.wikipedia.org/wiki/Murphy%27s_law)). Here are some tips that I learned through my experience in commercial and cultural (artistic) projects.

Communication
--------

### 5 minutes is 1 hour

During production, a programmer often says "give me 5 minutes" and most likely it will not be finished in 5 minutes. It is important to communicate well with other collaborators especially if they do not have experience in working with code. If I think it will take 5 minutes to implement, I would say it takes 1 hour (seriously), and if I finish early I will make coffee to enjoy the extra time.

This is important especially when working with dancers because they need to warm up and cannot wait on the stage for you to code. Usually I do not code when the dancers are ready to work and only take notes so that I can implement them after-hours.


### Work in tandem with designers

If you work with graphic designers, keep their expectation low and share the process step by step. If you let them design the whole graphics in Illustrator at once, you will be overwhelmed to implement everything, and the designers will be "disappointed" by the slightly different outcome because of limitations of what you can achieve with code. Start with simple components and reuse static images provided by them as much as possible. The key is to know each other's strength and to find the optimal collaborative workflow.


Optimizing the process
--------

### Avoid compilation and restart

I mostly work with Processing and sometimes with openFrameworks, and it is crucial to reduce the time of iteration to give immediate feedback to others. Live coding is the best way to realize this, and I found a few tricks to incorporate live coding (-ish tricks) in these frameworks.

**Move constant variables to JSON**: the simplest way is to define constant variables (e.g., window width/height, background color, button position) in a separate JSON file so that you do not need to recompile every time after you edit the parameters. It will be even more useful if this JSON file is dynamically loaded when the file is edited to "hot reload" to updated the parameter without restart. Also some versions of Processing support ["Tweak mode"](http://galsasson.com/tweakmode/), which achieves a similar result but within the standard editor.

**Use runtime interpreter**: For Processing, I made a ["framework"](https://github.com/micuat/liveJsP5js) that allows you to code in JavaScript similar to p5.js but interpreted by JavaScript interpreter on Java runtime, which enables to edit the code in real time. This significantly reduces the time of iteration. Also I made [a fork of ofxDuktape](https://github.com/micuat/ofxDuktape) which allows you to live code in openFrameworks using JavaScript. The fork provides (slightly) better support of function bindings but the capability is still quite limited, so I only use it to code specific tasks like motion graphics. When working with shaders, [ofxShader](https://github.com/patriciogonzalezvivo/ofxShader) can be helpful to hot reload shader code on runtime.


### Use more buffers

I learned from tools like vvvv or TouchDesigner that usually they assign plenty of off-screen buffer objects so that you can organize the graphics as layers (`ofFbo` or `PGraphics` in oF and p5). For example, I use a "main" buffer to render all the graphic components instead of directly drawing on the main window, and this "main" buffer is copied to the main window at the end of draw function. This helps keep drawing operations modular, and for example, easy to apply another shader to the final result.


### Make it modular

Not only graphics, keeping components as units helps avoid entire restart and initialization of software and hardware. This also enables to replace the hardware interface with a dummy input/output so you can simulate program without the hardware. For [a drawing robot](https://medium.com/@naoto_hieda/exhibition-open-closed-open-ff2c29b88cc0) I separated the program into units that communicate with each other. From the article I wrote:

*Because of the limited amount of time, my challenge was to test the program while the robot was not built. To solve this problem, I made the program modular so that some programs can be replaced by a dummy module. Raspberry Pi runs two node.js programs; one is to send commands to Arduino, and the other one is to convert high-level operations into low-level commands that can be sent to the Arduino. If the first node is executed as “offline mode,” it will ignore communication with Arduino and render results on the web interface instead. With express module for node.js, I could easily make a web interface for debug commands as well (move forward, etc), which is useful for testing on site.*


Test it extensively
--------

When things break, most likely it is because of lack of testing. There are 2 kinds of test that I suggest:

**1) Run the program for the duration of the performance or exhibition** to see if it crashes or lags because of, for example, memory leak. This durability test is rather easy to do; you simply need to run the whole setup over night. Preferably this should be iterated from the early phase of the project to eliminate all the possible issues; after some time of development, it may become very difficult to spot what is causing the error.

**2) Try the interaction in every possible "worst-case" scenarios**. It is often underestimated, but in case of a public exhibition, visitors will do the "worst" things, which you never imagined. Try to ask some friends to try the installation with no prior knowledge to see how they behave and how the program can potentially break.


But it still breaks
--------

After years of working with digital art, I always felt like walking on a tightrope and I decided not to trust technology or to embrace the glitches for my own creation. I define whatever happens on my desktop is part of creation and this notion liberated myself:

{% for category in site.categories %}
{% if category[0] == "project" %}
{% assign pages_list = category[1] %}
{% for post in pages_list %}
{% if post.title == "Tech is Nonbinary" %}
{% if group == null or group == post.group %}
{% assign fullwidth = true %}
{% include postbox.html %}
{% assign fullwidth = nil %}
{% endif %}
{% endif %}
{% endfor %}
{% assign pages_list = nil %}
{% assign group = nil %}
{% endif %}
{% endfor %}
