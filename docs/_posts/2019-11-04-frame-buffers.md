---
layout: post
title: "Frame Buffers"
categories: notes
---

When we draw something in OF, whether it's a circle or an image or a 3D shape, it gets drawn to the screen by default. But we don't need to necessarily draw to the screen; we can draw to an offscreen location called a *framebuffer*. '

A framebuffer object (or FBO) is a simple OpenGL object that lives in graphics memory that you can draw into. You can think of it as a "virtual window", anything you can draw on screen you can draw inside this window.

### ofFbo

In openFrameworks, the [`ofFbo`](https://openframeworks.cc/documentation/gl/ofFbo/) object is a wrapper around a framebuffer.
* You first need to create the `ofFbo` using [`allocate()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_allocate).
* You can turn it on or off using the [`begin()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_begin) and [`end()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_end) functions.
  * Anything you draw between the calls to `begin()` and `end()` will be drawn inside that `ofFbo`.
* An `ofFbo` actually renders to an `ofTexture`, and you can generally use an `ofFbo` anywhere you would use a texture.
  * You can draw the FBO directly to the screen using [`draw()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_draw).
  * You can retrieve the texture using [`getTexture()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_getTexture).

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawSimple.h %} 
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawSimple.cpp %}

The `ofFbo` object lives on the GPU. The data is available as an `ofTexture` only. `ofTexture` data can be read back to the CPU using the [`readToPixels()`](https://openframeworks.cc/documentation/gl/ofFbo/#show_readToPixels) function. This will store the data in the passed `ofPixels` object, which can then be used anywhere you would use any other pixel data, like with OpenCV. Note however that most systems are set up and optimized for CPU to GPU data transfer. While GPU to CPU does work, it should be used sparingly as it could slow down your application.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawContours.h %} 
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawContours.cpp %}

An `ofFbo` is a good way to position and scale an `ofTexture` before drawing it. Everything can be draw inside the FBO texture at native resolution, then transformed as a whole. This is particularly useful for drawing objects that don't have any scaling built-in, like `ofxCv::ContourFinder` for example.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawFull.h %} 
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-drawFull.cpp %}

### 3D

FBOs are also quite useful when working in 3D space, as they can provide a "window" into the 3D world. This window can then be manipulated just like any other `ofTexture`.

The following example uses a RealSense to generate a point cloud, and an `ofEasyCam` to display it in 3D. 

```cpp
camera.begin();
ofEnableDepthTest();
ofPushMatrix();

// Adjust points to match OF 3D space.
ofScale(100);
ofRotateXDeg(180);
rsDevice->getColorTex().bind();
rsDevice->getPointsMesh().draw();
rsDevice->getColorTex().unbind();

ofPopMatrix();
ofDisableDepthTest();
camera.end();
```

By default, the camera will take up the entire window resolution to draw the world in. This is less than ideal if we want to draw other elements in the window.

![]({{ site.baseurl }}/assets/images/fbo-overlap.png){:width="600px"}

We can pass an `ofRectangle` to [`ofCamera.begin()`](https://openframeworks.cc/documentation/3d/ofCamera/#show_begin) to tell it how to delimit the space it renders in.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-camViewport.h %} 
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-camViewport.cpp %}

While this works, we are still drawing directly in the app window, and would be unable to manipulate the camera render as a texture. A better approach is to draw the camera output directly in an `ofFbo`. Because we're using the entire frame buffer resolution for our drawing, we can revert to `ofCamera.begin()` without passing it a viewport.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-camFbo.h %} 
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-camFbo.cpp %}

You might notice that the camera controls are acting a little weird. This is because the `ofEasyCam` thinks that it's drawing in the bounds `(0, 0) to (640, 360)` as that is the size of the `ofFbo`, and it therefore limits the mouse controls to that area of the window. This can be adjusted by giving `ofEasyCam` actual bounds rectangle that the world is being drawn in using [`ofEasyCam.setControlArea()`](https://openframeworks.cc/documentation/3d/ofEasyCam/#show_setControlArea). This should make mouse control feel more natural.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-camControl.cpp %}

![]({{ site.baseurl }}/assets/images/fbo-quads.png){:width="600px"}

### Multi-window 

When working in installation settings, you will usually have a multi-display setup. A common setup is to have one monitor for controls and a preview window, and another display (either a large screen monitor or projector) for the main content. openFrameworks makes working with multiple windows easy, and includes some examples in `/path/to/OF/examples/windowing` demonstrating how this works.

The important thing to remember is that this needs to be configured before the `ofApp` starts running, therefore in the `main()` function found in `main.cpp`.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 main-multi.cpp %}

One of the simplest ways to set this up is to use a second draw function in the same `ofApp`, which will draw in the second window. Note that by just redrawing our FBOs in the second window, we don't need to re-render the contours or the point cloud, as this is already saved in the frame buffer texture.

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-multi.h %}
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-multi.cpp %}

### Blending

If we think of FBOs as layers, we can get interesting results by stacking them and applying different blend modes to them. OpenGL has built-in blend functions that are used to calculate the color of pixels that are overlaid in the same buffer, and these can be accessed using the [`ofEnableBlendMode()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofEnableBlendMode) global function. If you use Photoshop, some of these like `OF_BLENDMODE_ADD` and `OF_BLENDMODE_MULTIPLY` should sound familiar.

Let's update our RealSense example to render the point cloud in the projection window using custom blend modes.
* Set the RealSense alignment mode to `Align::Color` to ensure that depth, color, and point frames are in the same space.
* Use a new FBO to render a masked color image, by multiplying the threshold image by the full color image. This is done using `OF_BLENDMODE_MULTIPLY`. Note that this could have been achieved using OpenCV, but using blend modes happens almost automatically on the `ofTexture`!
* Draw the point cloud using the masked color image for texture, and `OF_BLENDMODE_SCREEN`. This ensures that color pixels override any black pixels and get drawn, even if they are behind the black pixels in 3D space. 

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-blend.h %}
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-blend.cpp %}

Finally, let's add some manual alpha blending on the projection output.
* Change the background in the points FBO from `ofBackground(0)` to [`ofClear(0, 0)`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofClear). `ofClear()` works like `ofBackground()` but can also take a second parameter for alpha. By clearing alpha to `0`, we are making the FBO texture transparent, which will make more interesting overlays.
* Add a call to [`ofSetBackgroundAuto(false)`](https://openframeworks.cc/documentation/graphics/ofGraphics/#show_ofSetBackgroundAuto) at the start of `drawProjection()`. This disables auto-clearing the window and will allow us to overlay textures over many frames. Note that you usually don't need to call `ofSetBackgroundAuto()` every frame, but since we are setting it for our second window, this is the best place to call this function.
* Draw the background color and the point cloud FBO using a variable alpha value to get different ghostly effects.

![]({{ site.baseurl }}/assets/images/fbo-fade.png){:width="600px"}

{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-fade.h %}
{% gist 308588ee21bd0478fba94c5bebf5c7a0 ofApp-fade.cpp %}
