---
layout: post
title: "Draw Bounds"
categories: notes
---

Most of the example apps we have built so far have had the same window size as the video grabber or depth camera size.

```cpp
ofSetWindowShape(640, 480);
grabber.setup(640, 480);
```

This is convenient because window coordinates match pixel coordinates, and we can easily do things like sampling using the mouse position without too much trouble.

However, it is also limiting as we can't define the window dimensions we want. We will usually want the video to take up the entire screen, especially for presentation purposes, so we need to get comfortable with image scaling.

### Scale and Aspect Ratio

Most OF classes with a draw method that takes an `x` and `y` usually can also accept a `width` and `height`. We could just set this to the window's width and height to have the image take up the entire available space.

{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp.h %} 
{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp-stretch.cpp %}

This is quick to implement but will stretch the image if its aspect ratio is different from the window's aspect ratio. If we want to keep the aspect ratio the same, we need to do a bit of math.

We can choose to match either the width or height of the image with the width or height of the window. We then compute the other dimension using the image's aspect ratio to avoid any stretching.

{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp-aspectFill.cpp %}

### ofRectangle

[`ofRectangle`](https://openframeworks.cc/documentation/types/ofRectangle/) is a very versatile openFrameworks type. It has a position and dimensions (`x`, `y`, `width`, `height`) and many useful functions for scaling, moving, drawing, checking inclusion, etc. 

We can use the [`ofRectangle.scaleTo()`](https://openframeworks.cc/documentation/types/ofRectangle/#show_scaleTo) to calculate image draw bounds while keeping the original aspect ratio. We do this by creating a new `ofRectangle` using the source image, then scaling it to a second `ofRectangle` representing the window dimensions. This can be done manually with
```cpp
ofRectangle windowBounds = ofRectangle(0, 0, ofGetWidth(), ofGetHeight());
```
or automatically by calling [`ofGetCurrentViewport()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofGetCurrentViewport).

{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp-scaleRect.cpp %}

### Transformation Matrix

Some classes do not allow parameters in their `draw()` function, and will always draw their content at `(0, 0)` and native resolution. 

In order to change these properties, we need to modify the surface we are drawing on, and not the object itself. You can think of this as keeping a pencil in place and moving the paper under it to draw (instead of keeping the paper still and moving the pencil over it).

These types of operations are called matrix transformations. We've already encountered them before when using [`ofScale()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofScale) and [`ofTranslate()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofTranslate) so this should feel familiar.

{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp-scaleMatrix.cpp %}

### Mapping

Even though we are changing the resolution of the drawn image, we are not changing the actual dimensions of the pixel data. A `640x480 px` frame still has `640x480 px` of data even when it is drawn at `1280x960 px`. 

We need to stay mindful of this when doing color look-ups, and make sure that we always remain in the image space when doing so. 

openFrameworks includes a useful [`ofMap()`](https://openframeworks.cc/documentation/math/ofMath/#!show_ofMap) function that easily maps values from one range to another. We can use this in the following example, where we use the mouse to pick the pixel color under the cursor of a scaled image. 

Note that we set the optional "clamp" parameter of `ofMap` to true to ensure that our pixel coordinate always stays within bounds, even when the mouse exits the window.

{% gist 9e17a69cb99aaecce48c91c13d43e1cd ofApp-mouseSample.cpp %}
