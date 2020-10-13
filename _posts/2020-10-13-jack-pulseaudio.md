---
layout: post
title:  "Jack to PulseAudio"
author: naoto
categories: [ report ]
tags: [ tutorial ]
image: assets/images/2020-10-13-jack-pulseaudio.jpg
description: "howto"
featured: true
comments: true
---

(image from [Jack](https://jackaudio.org/) website)

Ubuntu uses [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/) for the sound backend, and it works fine, for example, playing back audio on Chrome and listen on Bluetooth headphones. However, if you use a program that uses [Jack](https://jackaudio.org/) as a sound backend, e.g., SuperCollider, sound routing becomes messy. There are two problems 1) PulseAudio and Jack cannot access the same device and 2) Jack does not support Bluetooth devices.

Thanks to the advice from [Marco Donnarumma](https://twitter.com/MarcoDonnarumma) and [Aleksandr Yakunichev](https://twitter.com/yacodes), I decided to use Catia for routing. Installing Catia on Ubuntu 20.04 was not straightforward and it was a trial-and-error, so the following instructions might be missing some steps. Basically, you need to download and install KXStudio repo as stated [here](https://kx.studio/Repositories). As the website notes, I am not allowed to copy their instructions; nevertheless, I would add that you need to

    sudo apt update

in order to make `catia` available to be installed by apt.

Then open catia and launch jack. You will see system capture and playback. If you boot SuperCollider server, it will appear on the patchbay. Now load the Jack module on PulseAudio:

    pacmd load-module module-jack-source channels=2
    pacmd load-module module-jack-sink channels=2

which will appear on the patchbay as well. Now, for example, if you connect SuperCollider output to PulseAudio Jack source on the patchbay, the sound can be retrieved as a microphone input in Chrome. And if you connect PulseAudio Jack sink to SuperCollider input, sound from a Chrome tab can be input to SuperCollider.

Nevertheless, you will not hear SuperCollider (Jack) audio on the audio device connected to PulseAudio. This can be solved by loading a loopback module on PulseAudio:

    pacmd load-module module-loopback source=jack_in sink=XXX channels=2

where XXX is a sink name that can be found by `pacmd list-sinks | grep -e 'name:' -e 'index:'`.
