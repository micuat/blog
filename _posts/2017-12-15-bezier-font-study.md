---
layout: post
title:  "Bezier Font Study"
author: naoto
categories: [ sketch ]
tags: [ p5js, typography ]
image: assets/images/2017-12-15-Bezier-Font.png
description: "A sketch"
featured: true
comments: true
---

To be ported to p5.js.

abc.pde:
{% highlight java %}
import geomerative.*;
import java.util.ArrayList;

RShape shapes[][] = new RShape[26][2];
ArrayList<RPoint []> points = new ArrayList<RPoint []>();

void setup() {
  size(1200, 1200);
  RG.init(this);
  RCommand.setSegmentLength(20);
  initAbcShapes();
  for (int i = 0; i < shapes.length; i++) {
    if (shapes[i][1] == null)
      points.add(shapes[i][0].getPoints());
    else {
      RPoint [] p0 = shapes[i][0].getPoints();
      RPoint [] p1 = shapes[i][1].getPoints();
      RPoint [] p = new RPoint[p0.length + p1.length + 1];
      int count = 0;
      for (int j = 0; j < p0.length; j++, count++) {
        p[count] = p0[j];
      }
      p[count] = new RPoint(-1, -1);
      count++;
      for (int j = 0; j < p1.length; j++, count++) {
        p[count] = p1[j];
      }
      points.add(p);
    }
  }
}

void draw() {
  String str = "net neutrality is the\nprinciple that internet\nservice providers must\n";
  str+= "treat all data on the\ninternet the same and\nnot discriminate or";
  background(0);
  strokeWeight(7);
  noFill();
  stroke(255);

  translate(25, 400);
  scale(0.1, 0.1);
  pushMatrix();
  pushMatrix();
  for (int j = 0; j < str.length(); j++) {
    int c = (int)str.charAt(j);
    int n = c - 'a';
    if (0 <= n && n < 26) {
      float fract = (float)(frameCount) / 120.0f % 1;
      RPoint p[] = points.get(n);
      int iBegin = (int)constrain(map(fract, 0.55, 0.75, 0, p.length - 1), 0, p.length-1);
      int iEnd = (int)constrain(map(fract, 0, 0.2, 0, p.length - 1), 0, p.length-1);
      for (int i = iBegin; i < iEnd; i++) {
        if (p[i].x > 0 && p[i+1].x > 0)
          line(p[i].x, p[i].y, p[i+1].x, p[i+1].y);
      }
    }

    if (c == '\n') {
      popMatrix();
      translate(0, 500);
      pushMatrix();
    } else {
      translate(500, 0);
    }
  }
  popMatrix();
  popMatrix();

  str = "charge differently by\nuser content website\nplatform application\ntype of attached equipment\nor method of\ncommunication";
  pushMatrix();
  for (int j = 0; j < str.length(); j++) {
    int c = (int)str.charAt(j);
    int n = c - 'a';
    if (0 <= n && n < 26) {
      float fract = (float)(frameCount) / 120.0f % 1;
      RPoint p[] = points.get(n);
      int iBegin = (int)constrain(map(fract, 0.8, 1, 0, p.length - 1), 0, p.length-1);
      int iEnd = (int)constrain(map(fract, 0.25, 0.45, 0, p.length - 1), 0, p.length-1);
      for (int i = iBegin; i < iEnd; i++) {
        if (p[i].x > 0 && p[i+1].x > 0)
          line(p[i].x, p[i].y, p[i+1].x, p[i+1].y);
      }
    }

    if (c == '\n') {
      popMatrix();
      translate(0, 500);
      pushMatrix();
    } else {
      translate(500, 0);
    }
  }
  popMatrix();

  if (keyPressed && key == ' ') {
    saveFrame("test.png");
  }
}
{% endhighlight %}

strokes.pde:
{% highlight java %}
void initAbcShapes() {
  //a
  RShape s = new RShape();
  s.addMoveTo(113.0, 400.0);
  s.addBezierTo(118.0, 388.0, 119.0, 387.0, 113.0, 400.0);
  s.addBezierTo(107.0, 413.0, 197.0, 86.0, 245.0, 86.0);
  s.addBezierTo(293.0, 86.0, 374.0, 401.0, 377.0, 409.0);
  shapes[0][0] = s;
  s = new RShape();
  s.addMoveTo(161.0, 248.0);
  s.addBezierTo(161.0, 248.0, 327.0, 245.0, 327.0, 245.0);
  shapes[0][1] = s;

  //b
  s = new RShape();
  s.addMoveTo(126.0, 427.0);
  s.addBezierTo(126.0, 427.0, 126.0, 427.0, 126.0, 427.0);
  s.addBezierTo(126.0, 427.0, 122.0, 92.0, 122.0, 92.0);
  s.addBezierTo(122.0, 92.0, 217.0, 89.0, 255.0, 89.0);
  s.addBezierTo(293.0, 89.0, 333.0, 105.0, 334.0, 159.0);
  s.addBezierTo(335.0, 213.0, 270.0, 259.0, 258.0, 259.0);
  s.addBezierTo(246.0, 259.0, 125.0, 263.0, 125.0, 263.0);
  s.addBezierTo(125.0, 263.0, 212.0, 260.0, 258.0, 259.0);
  s.addBezierTo(304.0, 258.0, 332.0, 289.0, 333.0, 343.0);
  s.addBezierTo(334.0, 397.0, 288.0, 419.0, 264.0, 420.0);
  s.addBezierTo(240.0, 421.0, 125.0, 430.0, 125.0, 430.0);
  shapes[1][0] = s;
  
  //c
  s = new RShape();
  s.addMoveTo(332.0, 169.0);
  s.addBezierTo(329.0, 173.0, 346.0, 209.0, 332.0, 169.0);
  s.addBezierTo(318.0, 129.0, 297.0, 84.0, 220.0, 85.0);
  s.addBezierTo(143.0, 86.0, 110.0, 194.0, 109.0, 244.0);
  s.addBezierTo(108.0, 294.0, 142.0, 422.0, 234.0, 416.0);
  s.addBezierTo(326.0, 410.0, 323.0, 356.0, 330.0, 336.0);
  shapes[2][0] = s;
  
  //d
  s = new RShape();
  s.addMoveTo(126.0, 404.0);
  s.addBezierTo(126.0, 404.0, 126.0, 404.0, 126.0, 404.0);
  s.addBezierTo(126.0, 404.0, 121.0, 71.0, 121.0, 71.0);
  s.addBezierTo(121.0, 71.0, 185.0, 69.0, 262.0, 68.0);
  s.addBezierTo(339.0, 67.0, 384.0, 174.0, 387.0, 236.0);
  s.addBezierTo(390.0, 298.0, 341.0, 400.0, 270.0, 401.0);
  s.addBezierTo(199.0, 402.0, 126.0, 405.0, 126.0, 405.0);
  shapes[3][0] = s;
  
  //e
  s = new RShape();
  s.addMoveTo(332.0, 84.0);
  s.addBezierTo(332.0, 84.0, 332.0, 84.0, 332.0, 84.0);
  s.addBezierTo(332.0, 84.0, 121.0, 87.0, 121.0, 87.0);
  s.addBezierTo(121.0, 87.0, 122.0, 409.0, 122.0, 409.0);
  s.addBezierTo(122.0, 409.0, 339.0, 411.0, 339.0, 411.0);
  shapes[4][0] = s;
  s = new RShape();
  s.addMoveTo(121.0, 245.0);
  s.addBezierTo(121.0, 245.0, 333.0, 243.0, 333.0, 243.0);
  shapes[4][1] = s;
  
  //f
  s = new RShape();
  s.addMoveTo(332.0, 84.0);
  s.addBezierTo(332.0, 84.0, 332.0, 84.0, 332.0, 84.0);
  s.addBezierTo(332.0, 84.0, 121.0, 87.0, 121.0, 87.0);
  s.addBezierTo(121.0, 87.0, 122.0, 409.0, 122.0, 409.0);
  s.addBezierTo(122.0, 409.0, 121.0, 245.0, 121.0, 245.0);
  s.addBezierTo(121.0, 245.0, 333.0, 243.0, 333.0, 243.0);
  shapes[5][0] = s;
  
  //g
  s = new RShape();
  s.addMoveTo(371.0, 151.0);
  s.addBezierTo(373.0, 152.0, 377.0, 189.0, 371.0, 151.0);
  s.addBezierTo(365.0, 113.0, 351.0, 57.0, 255.0, 57.0);
  s.addBezierTo(159.0, 57.0, 130.0, 113.0, 128.0, 238.0);
  s.addBezierTo(126.0, 363.0, 188.0, 408.0, 251.0, 407.0);
  s.addBezierTo(314.0, 406.0, 371.0, 372.0, 372.0, 332.0);
  s.addBezierTo(373.0, 292.0, 373.0, 257.0, 373.0, 257.0);
  s.addBezierTo(373.0, 257.0, 373.0, 257.0, 373.0, 257.0);
  s.addBezierTo(373.0, 257.0, 256.0, 259.0, 256.0, 259.0);
  shapes[6][0] = s;
  
  //h
  s = new RShape();
  s.addMoveTo(111.0, 97.0);
  s.addBezierTo(111.0, 97.0, 111.0, 97.0, 111.0, 97.0);
  s.addBezierTo(111.0, 97.0, 114.0, 393.0, 114.0, 393.0);
  s.addBezierTo(114.0, 393.0, 112.0, 247.0, 112.0, 247.0);
  s.addBezierTo(112.0, 247.0, 333.0, 243.0, 333.0, 243.0);
  s.addBezierTo(333.0, 243.0, 330.0, 95.0, 330.0, 95.0);
  s.addBezierTo(330.0, 95.0, 337.0, 394.0, 337.0, 394.0);
  shapes[7][0] = s;
  
  //i
  s = new RShape();
  s.addMoveTo(175.0, 75.0);
  s.addBezierTo(175.0, 75.0, 175.0, 75.0, 175.0, 75.0);
  s.addBezierTo(175.0, 75.0, 298.0, 74.0, 298.0, 74.0);
  s.addBezierTo(298.0, 74.0, 237.0, 77.0, 237.0, 77.0);
  s.addBezierTo(237.0, 77.0, 240.0, 413.0, 240.0, 413.0);
  s.addBezierTo(240.0, 413.0, 177.0, 415.0, 177.0, 415.0);
  s.addBezierTo(177.0, 415.0, 294.0, 414.0, 294.0, 414.0);
  shapes[8][0] = s;
  
  //j
  s = new RShape();
  s.addMoveTo(236.0, 76.0);
  s.addBezierTo(236.0, 76.0, 236.0, 76.0, 236.0, 76.0);
  s.addBezierTo(236.0, 76.0, 371.0, 72.0, 371.0, 72.0);
  s.addBezierTo(371.0, 72.0, 307.0, 75.0, 307.0, 75.0);
  s.addBezierTo(307.0, 75.0, 316.0, 265.0, 315.0, 295.0);
  s.addBezierTo(314.0, 325.0, 289.0, 399.0, 219.0, 401.0);
  s.addBezierTo(149.0, 403.0, 104.0, 352.0, 101.0, 278.0);
  shapes[9][0] = s;
  
  //k
  s = new RShape();
  s.addMoveTo(130.0, 97.0);
  s.addBezierTo(130.0, 97.0, 130.0, 97.0, 130.0, 97.0);
  s.addBezierTo(130.0, 97.0, 138.0, 440.0, 139.0, 439.0);
  s.addBezierTo(140.0, 438.0, 134.0, 264.0, 134.0, 264.0);
  s.addBezierTo(134.0, 264.0, 332.0, 96.0, 332.0, 96.0);
  s.addBezierTo(332.0, 96.0, 134.0, 266.0, 134.0, 266.0);
  s.addBezierTo(134.0, 266.0, 348.0, 429.0, 348.0, 429.0);
  shapes[10][0] = s;
  
  //l
  s = new RShape();
  s.addMoveTo(118.0, 93.0);
  s.addBezierTo(118.0, 93.0, 118.0, 93.0, 118.0, 93.0);
  s.addBezierTo(118.0, 93.0, 127.0, 424.0, 127.0, 424.0);
  s.addBezierTo(127.0, 424.0, 355.0, 417.0, 355.0, 417.0);
  shapes[11][0] = s;
  
  //m
  s = new RShape();
  s.addMoveTo(65.0, 392.0);
  s.addBezierTo(65.0, 392.0, 65.0, 392.0, 65.0, 392.0);
  s.addBezierTo(65.0, 392.0, 142.0, 97.0, 142.0, 97.0);
  s.addBezierTo(142.0, 97.0, 245.0, 394.0, 245.0, 394.0);
  s.addBezierTo(245.0, 394.0, 322.0, 93.0, 322.0, 93.0);
  s.addBezierTo(322.0, 93.0, 410.0, 404.0, 410.0, 404.0);
  shapes[12][0] = s;
  
  //n
  s = new RShape();
  s.addMoveTo(110.0, 395.0);
  s.addBezierTo(110.0, 395.0, 110.0, 395.0, 110.0, 395.0);
  s.addBezierTo(110.0, 395.0, 116.0, 90.0, 116.0, 90.0);
  s.addBezierTo(116.0, 90.0, 351.0, 403.0, 351.0, 403.0);
  s.addBezierTo(351.0, 403.0, 357.0, 92.0, 357.0, 92.0);
  shapes[13][0] = s;
  
  //o
  s = new RShape();
  s.addMoveTo(227.0, 401.0);
  s.addBezierTo(227.0, 401.0, 334.0, 401.0, 227.0, 401.0);
  s.addBezierTo(120.0, 401.0, 103.0, 346.0, 103.0, 243.0);
  s.addBezierTo(103.0, 140.0, 129.0, 85.0, 228.0, 85.0);
  s.addBezierTo(327.0, 85.0, 352.0, 160.0, 351.0, 244.0);
  s.addBezierTo(350.0, 328.0, 346.0, 400.0, 226.0, 402.0);
  shapes[14][0] = s;
  
  //p
  s = new RShape();
  s.addMoveTo(117.0, 401.0);
  s.addBezierTo(117.0, 401.0, 117.0, 400.0, 117.0, 401.0);
  s.addBezierTo(117.0, 402.0, 113.0, 81.0, 113.0, 81.0);
  s.addBezierTo(113.0, 81.0, 113.0, 82.0, 113.0, 82.0);
  s.addBezierTo(113.0, 82.0, 206.0, 80.0, 253.0, 80.0);
  s.addBezierTo(300.0, 80.0, 332.0, 115.0, 333.0, 179.0);
  s.addBezierTo(334.0, 243.0, 287.0, 255.0, 255.0, 256.0);
  s.addBezierTo(223.0, 257.0, 116.0, 258.0, 116.0, 258.0);
  shapes[15][0] = s;
  
  //q
  s = new RShape();
  s.addMoveTo(227.0, 401.0);
  s.addBezierTo(227.0, 401.0, 334.0, 401.0, 227.0, 401.0);
  s.addBezierTo(120.0, 401.0, 103.0, 346.0, 103.0, 243.0);
  s.addBezierTo(103.0, 140.0, 129.0, 85.0, 228.0, 85.0);
  s.addBezierTo(327.0, 85.0, 352.0, 160.0, 351.0, 244.0);
  s.addBezierTo(350.0, 328.0, 346.0, 400.0, 226.0, 402.0);
  shapes[16][0] = s;
  s = new RShape();
  s.addMoveTo(286.0, 332.0);
  s.addBezierTo(286.0, 332.0, 286.0, 332.0, 286.0, 332.0);
  s.addBezierTo(286.0, 332.0, 357.0, 410.0, 357.0, 410.0);
  shapes[16][1] = s;
  
  //r
  s = new RShape();
  s.addMoveTo(146.0, 403.0);
  s.addBezierTo(146.0, 403.0, 146.0, 403.0, 146.0, 403.0);
  s.addBezierTo(146.0, 403.0, 139.0, 75.0, 139.0, 75.0);
  s.addBezierTo(139.0, 75.0, 139.0, 75.0, 139.0, 75.0);
  s.addBezierTo(139.0, 75.0, 268.0, 73.0, 304.0, 73.0);
  s.addBezierTo(340.0, 73.0, 385.0, 113.0, 386.0, 159.0);
  s.addBezierTo(387.0, 205.0, 347.0, 250.0, 305.0, 250.0);
  s.addBezierTo(263.0, 250.0, 142.0, 254.0, 142.0, 254.0);
  s.addBezierTo(142.0, 254.0, 372.0, 412.0, 372.0, 412.0);
  shapes[17][0] = s;
  
  //s
  s = new RShape();
  s.addMoveTo(355.0, 188.0);
  s.addBezierTo(351.0, 198.0, 337.0, 205.0, 355.0, 188.0);
  s.addBezierTo(373.0, 171.0, 376.0, 148.0, 367.0, 118.0);
  s.addBezierTo(358.0, 88.0, 314.0, 60.0, 259.0, 60.0);
  s.addBezierTo(204.0, 60.0, 126.0, 113.0, 132.0, 166.0);
  s.addBezierTo(138.0, 219.0, 204.0, 247.0, 245.0, 266.0);
  s.addBezierTo(286.0, 285.0, 359.0, 294.0, 371.0, 351.0);
  s.addBezierTo(383.0, 408.0, 356.0, 449.0, 262.0, 452.0);
  s.addBezierTo(168.0, 455.0, 117.0, 384.0, 117.0, 338.0);
  shapes[18][0] = s;
  
  //t
  s = new RShape();
  s.addMoveTo(124.0, 99.0);
  s.addBezierTo(124.0, 99.0, 124.0, 99.0, 124.0, 99.0);
  s.addBezierTo(124.0, 99.0, 396.0, 95.0, 396.0, 95.0);
  s.addBezierTo(396.0, 95.0, 255.0, 97.0, 255.0, 97.0);
  s.addBezierTo(255.0, 97.0, 260.0, 403.0, 260.0, 403.0);
  shapes[19][0] = s;
  
  //u
  s = new RShape();
  s.addMoveTo(116.0, 103.0);
  s.addBezierTo(116.0, 103.0, 116.0, 103.0, 116.0, 103.0);
  s.addBezierTo(116.0, 103.0, 118.0, 219.0, 121.0, 279.0);
  s.addBezierTo(124.0, 339.0, 163.0, 414.0, 234.0, 409.0);
  s.addBezierTo(305.0, 404.0, 326.0, 345.0, 328.0, 275.0);
  s.addBezierTo(330.0, 205.0, 328.0, 99.0, 328.0, 99.0);
  shapes[20][0] = s;
  
  //v
  s = new RShape();
  s.addMoveTo(113.0, 126.0);
  s.addBezierTo(113.0, 126.0, 113.0, 126.0, 113.0, 126.0);
  s.addBezierTo(113.0, 126.0, 239.0, 411.0, 239.0, 411.0);
  s.addBezierTo(239.0, 411.0, 365.0, 127.0, 365.0, 127.0);
  shapes[21][0] = s;
  
  //w
  s = new RShape();
  s.addMoveTo(74.0, 106.0);
  s.addBezierTo(74.0, 106.0, 74.0, 106.0, 74.0, 106.0);
  s.addBezierTo(74.0, 106.0, 145.0, 410.0, 145.0, 410.0);
  s.addBezierTo(145.0, 410.0, 240.0, 104.0, 240.0, 104.0);
  s.addBezierTo(240.0, 104.0, 319.0, 417.0, 319.0, 417.0);
  s.addBezierTo(319.0, 417.0, 400.0, 105.0, 400.0, 105.0);
  shapes[22][0] = s;
  
  //x
  s = new RShape();
  s.addMoveTo(108.0, 102.0);
  s.addBezierTo(108.0, 102.0, 108.0, 102.0, 108.0, 102.0);
  s.addBezierTo(108.0, 102.0, 349.0, 399.0, 349.0, 399.0);
  shapes[23][0] = s;
  s = new RShape();
  s.addMoveTo(351.0, 100.0);
  s.addBezierTo(351.0, 100.0, 351.0, 100.0, 351.0, 100.0);
  s.addBezierTo(351.0, 100.0, 100.0, 398.0, 100.0, 398.0);
  shapes[23][1] = s;
  
  //y
  s = new RShape();
  s.addMoveTo(116.0, 81.0);
  s.addBezierTo(116.0, 81.0, 116.0, 81.0, 116.0, 81.0);
  s.addBezierTo(116.0, 81.0, 241.0, 238.0, 241.0, 238.0);
  shapes[24][0] = s;
  s = new RShape();
  s.addMoveTo(357.0, 78.0);
  s.addBezierTo(357.0, 78.0, 357.0, 78.0, 357.0, 78.0);
  s.addBezierTo(357.0, 78.0, 242.0, 238.0, 242.0, 238.0);
  s.addBezierTo(242.0, 238.0, 242.0, 430.0, 242.0, 431.0);
  shapes[24][1] = s;
  
  //z
  s = new RShape();
  s.addMoveTo(113.0, 110.0);
  s.addBezierTo(113.0, 110.0, 113.0, 110.0, 113.0, 110.0);
  s.addBezierTo(113.0, 110.0, 370.0, 106.0, 370.0, 106.0);
  s.addBezierTo(370.0, 106.0, 106.0, 403.0, 106.0, 403.0);
  s.addBezierTo(106.0, 403.0, 390.0, 409.0, 390.0, 409.0);
  shapes[25][0] = s;
}
{% endhighlight %}
