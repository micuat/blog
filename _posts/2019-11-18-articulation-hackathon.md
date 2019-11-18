---
layout: post
title:  "Articulation Hackathon"
author: naoto
categories: [ report ]
#tags: [red, yellow]
image: assets/images/2019-11-15-articulation.png
description: "A report about Articulation Hackathon"
featured: true
#hidden: true
#rating: 4.5
comments: false
---

A multidisciplinary hackathon by the [Articulations](https://labodanse.org/articulations/) team took place on November 14th and 15th, 2019 at CNRS in Paris, France, after [a two-day symposium at ENSAD](https://www.facebook.com/events/2467360340252026/). The focus of the hackathon is to experiment with Articulations, a virtual reality environment platform, and the participants were a mixed group of neuroscientists, designers, artists, dancers, anthropologists, engineers, and of course, people who have more than one profession.

The first day started with a movement session by [Clint Lutes](https://clintlutes.com/), followed by several brainstorming sessions. During the movement session, we were instructed to walk around and to meet people in the space, and we ended up creating a "virtual reality" environment with our bodies without talking to each other. Then, we started meeting people we do not know each other and talking about ideas for the hackathon. I tried to talk both in English and in French, which I failed because of my French level, but this led to an idea of creating a virtual environment for miscommunication (on which I did not work during the hackathon, but I still think that it is an interesting idea). After the first brainstorming round, each participant wrote down a name of their idea on a piece of paper, and we tried to map them on the floor based on the relevance of the projects. [Adrien Gaumé](https://sidelaner01.wixsite.com/butohdelight) had an idea of relating the 2D space to the 3D space of the body and thus he put his paper on the wall to emphasize the dimensionality; which some of us followed too.

The map of the ideas was grouped into, for example, unbodiment, tracing, interactive installations, and distortion. We spent some time talking within the groups, and later we all came back together to present ideas. I was moving between two groups but ended up creating a VR environment filled with cats as a joke (but indeed it has a question of cats animating as autonomous agents and as sound sources in the virtual space).

![Cat VR screenshot](/blog/assets/images/2019-11-15-catvr.png)

On the second day, Loup Vuarnesson from ENSAD gave a tutorial of Unity. We started from an empty scene and introduced a physics engine. Some of the enthusiastic participants started writing their own scripts in C#.

Then, we formed four new groups based on the projects from the first day: augmented reality, reactive sound system, physiological sensors, and storytelling. I joined the storytelling group and made a few demos. Because VR headsets were taken by the other groups, we decided to use a projector for a screen and a Kinect for the interaction. Mainly we worked on the hybrid space of spheres which move based on recorded data and lines which react to the live tracking data (top photo). Not only the movements, the dancers or the people outside the stage narrate the visuals to develop a story of the geometric shapes.

With [Cristina Hoffmann](http://cristinahoffmann.com/), we created an embodied poetry experience by attaching words to the skeletal tracking of the body. By moving the body, the mover can literally shape the text into a new form. Currently, the vocabulary is fixed, but we can easily imagine showing random words or based on an algorithm.

![Kinect with words](/blog/assets/images/2019-11-15-words.png)

Other groups presented a VR environment reacting to heartrate, a concept of interactive sound installation, and a VR system with a tracked pico projector to reveal the virtual environment in the real space. The hackathon was exceptional that we spent more time on the ideation rather than coding. This format suits well to me as I usually bring tools (in this case, Kinect visualization integrated with Processing and Unity) to a hackathon and spend time to glue these tools together to realize the ideas instead of making a library from scratch.
