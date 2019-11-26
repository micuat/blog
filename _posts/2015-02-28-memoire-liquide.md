---
layout: post
title:  "Mémoire Liquide"
author: naoto
categories: [ project ]
tags: [ installation ]
image: assets/images/2015-02-28-memoire-liquide.jpg
description: "Mémoire Liquide"
featured: true
comments: true
---

<iframe src="https://player.vimeo.com/video/130157438" width="560" height="315" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<iframe width="560" height="315" src="https://www.youtube.com/embed/xAWZ4DOHXgM?rel=0" frameborder="0" allowfullscreen></iframe>

The installation includes three computers: a body projection PC, a front projection PC and a back projection PC.

Participants stand on the stage, and their skeletons are tracked by Kinect and OpenNI+NiTE (<a href="https://github.com/SolitaryCipher/ofxOpenNI" target="_blank">ofxOpenNI</a>)
on the body projection PC.
The body projection PC is connected to a projector facing to the stage which is calibrated by <a href="https://github.com/micuat/mapamokinect" target="_blank">customized mapamok</a>.
Tracked hand coordinates are warped to the projector coordinate system,
and fluid is generated on the warped position with an initial velocity corresponding to the hand velocity (<a href="https://github.com/patriciogonzalezvivo/ofxFluid" target="_blank">ofxFluid</a>).
Thus, virtual fluid is projected on the hands and the residual can be projected onto the body.
To avoid projection on faces, black masks are overlaid on the faces tracked by NiTE.

The hand coordinates are sent to the front projection PC through OSC for projection on a sculpture.
This PC renders similar fluid simulation.
OSC is proxied to a Unity3d app which renders ripples by a customized water asset.
The scuplture projection PC blends fluid and ripples by Syphon and Resolume, which can be easily controlled by the artists.
Finally, the blended video is warped by MadMapper for projection.

The back projection PC projects user contributed videos and images on the other side of the sculpture.

<a href="https://www.facebook.com/Memoireliquideart" target="_blank">Facebook</a>  
<a href="https://sat.qc.ca/fr/evenements/nuit-blanche-2015" target="_blank">SAT</a>
