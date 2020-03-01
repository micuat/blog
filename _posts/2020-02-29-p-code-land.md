---
layout: post
title:  "p-code Land"
author: naoto
categories: [ platform ]
tags: [ threejs, curation ]
image: assets/images/2020-02-29-p-code-land.jpg
description: "p-code"
featured: true
comments: true
---

[open](https://p-code-land.glitch.me/)

p-code Land is an experimental virtual space for collective music live-coding. The syntax is based on [p-code](https://github.com/p-code-magazine/p-code) but numbers denote MIDI notes not frequencies, and some features are added.

For the syntax and further information, please visit p-code-oreore:

{% for category in site.categories %}
{% if category[0] == "platform" %}
{% assign pages_list = category[1] %}
{% for post in pages_list %}
{% if post.title == "p-code oreore" %}
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
