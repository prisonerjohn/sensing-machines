---
layout: post
title: "Images and Video"
categories: notes
---

# Image Data

Digital images come in a variety of formats, each with their own properties.

## Vector Graphics

*Vector* formats define a set of points and instructions on how to draw them. The instructions are run by a program to raster the image in order to view it. 

Some of the more common vector formats are SVG, EPS, PDF, and AI.

If we open the following SVG file in a text editor, we will notice that it is fairly easy to read the format. It almost reads like a Processing program :)

![](/assets/images/shapes.svg){:height="360px" width="360px"}

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg
   ...
   height="512"
   width="512">
  ...
  <g
     transform="translate(0,-161.53332)"
     id="layer1">
    <circle
       style="stroke-width:0.26458332;fill:#00ffff;fill-opacity:1"
       r="52.916664"
       cy="229.26665"
       cx="67.73333"
       id="path3713" />
    <rect
       y="228.20831"
       x="5.2916665"
       height="63.5"
       width="63.5"
       id="rect4520"
       style="fill:#ff0000;fill-opacity:1;stroke-width:0.25843021" />
    <path
       id="path4524"
       d="M 49.514879,171.88985 123.5982,282.2589 Z"
       style="fill:none;stroke:#00b400;stroke-width:2.64583325;stroke-linecap:butt;stroke-linejoin:miter;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1" />
  </g>
</svg>
```

Some advantages of vector graphics are that you can easily scale the image without any quality loss, and without an increase in file size. This is because the instruction set does not change, the only thing that changes is the point values.
