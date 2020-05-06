---
layout: post
title:  "Architecture Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js, tinysketch ]
image: assets/images/2020-05-06-architecture-study-1.png
description: "A sketch"
featured: true
comments: true
threejs: true
---

<div id = "threesketch">
</div>

<script type="module">
// Naoto Hieda
// https://creativecommons.org/licenses/by-sa/3.0/

import { GLTFLoader } from '{{ site.baseurl }}/assets/js/GLTFLoader.js';

const loader = new GLTFLoader().setPath( '{{ site.baseurl }}/assets/models/' );
loader.load( '2020-05-06-architecture-study-1.glb', function ( gltf ) {
  scene.add( gltf.scene );
  render();
});

const rect = document.getElementById('threesketch').getBoundingClientRect();

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  50,
  rect.width / rect.height,
  0.1,
  1000
);

camera.position.set(20, 4, 5);
const renderer = new THREE.WebGLRenderer();
renderer.setClearColor(0xdfdfdf);
renderer.setPixelRatio(window.devicePixelRatio);
renderer.setSize(rect.width, rect.height);
document.getElementById("threesketch").appendChild(renderer.domElement);

const directionalLight = new THREE.DirectionalLight(0xffffff, 0.7);
scene.add(directionalLight);

const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

function render() {
  requestAnimationFrame(render);

  let timer = Date.now() * 0.001;

  camera.position.x = Math.cos(timer * 0.2) * 20;
  camera.position.z = Math.sin(timer * 0.2) * 20;

  camera.lookAt(scene.position);

  renderer.render(scene, camera);
}
// render();
</script>
