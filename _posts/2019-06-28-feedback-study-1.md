---
layout: post
title:  "Feedback Study 1"
author: naoto
categories: [ sketch ]
tags: [ p5js ]
image: assets/images/2019-06-28-feedback-study-1.png
description: "A sketch"
featured: true
comments: true
threejs: true
---

<div id = "p5sketch">
<!-- p5 instance will be created here -->
</div>
<video id="video" loop webkit-playsinline style="display:none">
</video>

An experiment created with Janine Harrington. Click to start.

<script id="fragShader" type="shader-code">
uniform vec2 res;//The width and height of our screen
uniform sampler2D bufferTexture;//Our input texture
uniform sampler2D videoTexture;
uniform float time;
void main() {
  vec2 st = gl_FragCoord.xy / res;
  vec2 uv = st - vec2(0.5);
  //uv *= 0.995;
  uv += vec2(0.5);

  vec4 sum = texture2D(bufferTexture, uv);
  vec4 src = texture2D(videoTexture, uv);
  sum.rgb = mix(sum.rbg, src.rgb, 0.01);
  gl_FragColor = sum;
  
}
</script>
<script>
var scene;
var camera;

var renderer;
var bufferScene;
var textureA;
var textureB;
var bufferMaterial;
var plane;
var bufferObject;
var finalMaterial;
var quad;
var video;
var videoTexture;

document.addEventListener('click', function (event) {
  if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
    var constraints = { video: { width: 1280, height: 720, facingMode: 'user' } };
    navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
      // apply the stream to the video element used in the texture
      video.srcObject = stream;
      video.play();
    }).catch(function (error) {
      console.error('Unable to access the camera/webcam.', error);
    });
  } else {
    console.error('MediaDevices interface not available.');
  }
});

function sceneSetup() {
  scene = new THREE.Scene();
  const rect = document.getElementById('p5sketch').getBoundingClientRect();
  var width = rect.width;//window.innerWidth;
  var height = rect.width * 0.75;//window.innerHeight;
  camera = new THREE.OrthographicCamera(width / - 2, width / 2, height / 2, height / - 2, 1, 1000);
  camera.position.z = 2;

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  document.getElementById('p5sketch').appendChild(renderer.domElement);
}

function videoTextureSetup() {
  video = document.getElementById('video');
  videoTexture = new THREE.VideoTexture(video);

  videoTexture.minFilter = THREE.LinearFilter;
  videoTexture.magFilter = THREE.LinearFilter;
  videoTexture.format = THREE.RGBFormat;
}

function bufferTextureSetup() {
  //Create buffer scene
  bufferScene = new THREE.Scene();
  //Create 2 buffer textures
  textureA = new THREE.WebGLRenderTarget(window.innerWidth, window.innerHeight, { minFilter: THREE.LinearFilter, magFilter: THREE.NearestFilter });
  textureB = new THREE.WebGLRenderTarget(window.innerWidth, window.innerHeight, { minFilter: THREE.LinearFilter, magFilter: THREE.NearestFilter });
  textureA.minFilter = THREE.LinearFilter;
  textureA.magFilter = THREE.LinearFilter;
  textureB.minFilter = THREE.LinearFilter;
  textureB.magFilter = THREE.LinearFilter;
  //Pass textureA to shader
  bufferMaterial = new THREE.ShaderMaterial({
    uniforms: {
      bufferTexture: { type: "t", value: textureA.texture },
      res: { type: 'v2', value: new THREE.Vector2(window.innerWidth, window.innerHeight) },
      //Keeps the resolution
      videoTexture: { type: "t", value: videoTexture },
      time: { type: "f", value: Math.random() * Math.PI * 2 + Math.PI }
    },
    fragmentShader: document.getElementById('fragShader').innerHTML
  });
  plane = new THREE.PlaneBufferGeometry(window.innerWidth, window.innerHeight);
  bufferObject = new THREE.Mesh(plane, bufferMaterial);
  bufferScene.add(bufferObject);

  //Draw textureB to screen 
  finalMaterial = new THREE.MeshBasicMaterial({ map: textureB });
  quad = new THREE.Mesh(plane, finalMaterial);
  scene.add(quad);
}

function render() {

  requestAnimationFrame(render);

  //Draw to textureB
  renderer.render(bufferScene, camera, textureB, true);

  //Swap textureA and B
  var t = textureA;
  textureA = textureB;
  textureB = t;
  quad.material.map = textureB.texture;
  bufferMaterial.uniforms.bufferTexture.value = textureA.texture;

  //Update time
  bufferMaterial.uniforms.time.value += 0.01;

  //Finally, draw to the screen
  renderer.render(scene, camera);
}

//Initialize the Threejs scene
sceneSetup();
//Setup the frame buffer/texture we're going to be rendering to instead of the screen
videoTextureSetup();

bufferTextureSetup();

render();
</script>
