---
layout: post
title: "Depth World"
categories: notes
---

### 3D in OF

Everything we have done so far has been in two dimensions, using `(x, y)` for position, and `[width, height]` for size. Now that we've got data that also has depth, we can move into the third dimension to represent it. This means using `(x, y, z)` for position, and `[width, height, depth]` for size.

By default, the openFrameworks canvas is set to 2D. The origin `(0, 0)` is in the top-left, and values increase as you move right and down. In 3D, the origin `(0, 0, 0)` is in the middle of the window. You can move in both directions in any of the three dimensions, meaning that you can have positive and negative values for positions. 

Note that in 3D, the Y direction is the opposite than in 2D; Y increases as you move up. This is common with most 3D software.

The Z direction, however, can follow one of two coordinate system conventions:
* A *right-handed* coordinate system means the z-axis points forward. Z decreases as you move further away.
* A *left-handed* coordinate system means the z-axis points backwards. Z increases as you move further away.

openFrameworks follows the OpenGL convention and has a right-handed coordinate system.

![]({{ site.baseurl }}/assets/images/axis.png){:width="600px"}

### Cameras

To switch to 3D space, the simplest way is to use an [`ofCamera`](https://openframeworks.cc/documentation/3d/ofCamera/). 

* `ofCamera` has [`begin()`](https://openframeworks.cc/documentation/3d/ofCamera/#show_begin) and [`end()`](https://openframeworks.cc/documentation/3d/ofCamera/#show_end) functions; anything you put in between those will be draw in 3D space.
* The camera needs to be positioned somewhere in space. We use [`setPosition()`](https://openframeworks.cc/documentation/3d/ofNode/#show_setPosition) to place it. 

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-camera.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-camera.cpp %}

Note the use of `glm::vec3` for the camera position attribute. [`glm`](https://openframeworks.cc/documentation/glm/) is the mathematics library used by default in OF. Here we are using the `glm::vec3` type which is a 3D point (with x, y, z coordinates). 

* Alternatively to `setPosition()`, there are also functions for [`truck()`](https://openframeworks.cc/documentation/3d/ofNode/#show_truck), [`boom()`](https://openframeworks.cc/documentation/3d/ofNode/#show_boom) (aka pedestal), and [`dolly()`](https://openframeworks.cc/documentation/3d/ofNode/#show_dolly) to move in the XYZ axes.
* A camera can be oriented anywhere in space as well. We can set its orientation with [`setOrientation()`](https://openframeworks.cc/documentation/3d/ofNode/#show_setOrientation). 
* There are also functions for [`panDeg()`](https://openframeworks.cc/documentation/3d/ofNode/#show_panDeg), [`tiltDeg()`](https://openframeworks.cc/documentation/3d/ofNode/#show_tiltDeg) (aka pedestal), and [`rollDeg()`](https://openframeworks.cc/documentation/3d/ofNode/#show_rollDeg) to rotate in the XYZ axes. 

<div style="width:600px;margin:0 auto;" markdown="1">
[![The names of types of camera movements](https://external-preview.redd.it/dgHPuSpFv8RvvFbQZW4zWCW3AAwTuW7yZog8g22F9bo.png?auto=webp&s=2acd6490a7e5b188871b80f52e678ded349d0811)
*The names of types of camera movements*](https://www.reddit.com/r/LearnUselessTalents/comments/5jpd54/the_names_of_types_of_camera_movements/)
</div>

* It is sometimes more useful to tell a camera where to "look" rather than how to orient itself. This is done with [`lookAt()`](https://openframeworks.cc/documentation/3d/ofNode/#show_lookAt) which takes a target position as an argument. The camera will figure out automatically what orientation to use to look at that target. 

The following example demonstrates the effect of these different functions on the `ofCamera`.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-transform.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-transform.cpp %}

Controlling an `ofCamera` using sliders is not always intuitive, so OF provides [`ofEasyCam`](https://openframeworks.cc/documentation/3d/ofEasyCam/) as an alternative. `ofEasyCam` is an `ofCamera` that is controlled using the mouse, and makes navigating the scene extremely easy. If you've every used 3D software like Maya or played FPS video games, controlling the camera should feel familiar.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-easyCam.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-easyCam.cpp %}

### Meshes

Everything that is drawn on screen is *geometry*.
* The geometry has a *topology*, which is what the geometry is made up of. This is usually *points*, *lines*, or *triangles*. These can have different arrangements, as seen in the diagram below.
* The topology is made up of *vertices*. These are points with specific parameters. A simple *vertex* will just have a position. A more complex *vertex* can also have a color, a normal, texture coordinates, etc.
* The *topology* and *vertex* data together make up a *mesh*. A *mesh* is like a series of commands that tell the application how to draw the geometry to the screen.

In openFrameworks, we use [`ofMesh`](https://openframeworks.cc/documentation/3d/ofMesh/) as the geometry container. This is what is used to draw meshes to the screen.

<div style="width:600px;margin:0 auto;" markdown="1">
[![3D Graphics with OpenGL](https://www.ntu.edu.sg/home/ehchua/programming/opengl/images/GL_GeometricPrimitives.png)
*3D Graphics with OpenGL*](https://www.ntu.edu.sg/home/ehchua/programming/opengl/CG_BasicsTheory.html)
</div>

Every time we have been drawing images, we've actually been drawing two triangles in the shape of a rectangle.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-mesh.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-meshPos.cpp %}

![]({{ site.baseurl }}/assets/images/quad-wires.png){:width="600px"}

If we assign each vertex a color, we can see how that gets rendered across the geometry.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-meshColor.cpp %}

Even though we only have four vertices and four colors, the entire window has color across it. When using topology `OF_PRIMITIVE_TRIANGLES`, the entire shape of the triangle is filled in, so the renderer calculates the color for each pixel on screen by mixing the vertex colors together. This is called *interpolation*.

![]({{ site.baseurl }}/assets/images/quad-colors.png){:width="600px"}

Interpolation doesn't just work with colors, but with all vertex parameters. This is why when we use two points to draw a line, the entire length of the line gets drawn and not just the two end points.

Let's replace the colors per vertex by texture coordinates. Instead of setting the color explicitly, this tells the renderer to pick the color from a texture we assign. 
* This is called texture *binding*. 
* Texture coordinates are 2D, so we will use `glm::vec2` to define them.
* All objects in OF that render to the screen have [`bind()`](https://openframeworks.cc/documentation/gl/ofTexture/#show_bind) / [`unbind()`](https://openframeworks.cc/documentation/gl/ofTexture/#show_unbind) methods which are used for this! 
* This includes `ofImage`, `ofVideoGrabber`, and basically anything that contains an `ofTexture`.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-meshTex.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-meshTex.cpp %}

The last example does essentially what `ofVideoGrabber.draw()` does:
1. Generate a mesh with vertex positions and texture coordinates.
1. Bind the texture provided by the video grabber.
1. Draw the mesh to the screen using the bound texture to set the pixel colors.

<div style="width:600px;margin:0 auto;" markdown="1">
<iframe src="https://player.vimeo.com/video/141405962?color=ffffff&byline=0&portrait=0" width="640" height="360" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>
*<a href="https://vimeo.com/141405962">Lunar Surface The Incinerator - Installation</a> by <a href="https://www.kimchiandchips.com/works/">Kimchi and Chips</a>.*
</div>

### Point Clouds

If we change our mesh topology to `OF_PRIMITIVE_POINTS`, this will draw points at each vertex position without interpolating in the space between them.

If we want to add more resolution, we need more points. Let's update our mesh to add a vertex for every pixel.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-points.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-points.cpp %}

This still looks almost identical because we're drawing a point at every pixel position, essentially leaving no gaps. 

Let's add a skip parameter to modify the distance between points.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-skipPoints.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-skipPoints.cpp %}

Instead of setting the `z` position of each vertex to `0`, we can use some meaningful data. Let's switch input from `ofVideoGrabber` to a depth camera, and use the depth data from our sensor! 

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-pointCloud.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-pointCloud.cpp %}

Note the use of [`ofEnableDepthTest()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofEnableDepthTest) inside the camera block. This is to make sure that our points' distance from the camera is taken into account when rendering. 
* With depth test on, geometry is sorted before rendering so that points that are nearer than others will be drawn on top of them.
* When depth test is off (which is how we've been drawing so far), geometry is sorted in the order it is drawn to the screen, without taking its position in space into account.

![]({{ site.baseurl }}/assets/images/rs-pointsgray.png){:width="600px"}

### Correspondence

At this point, you may be tempted to swap out the bind from the depth texture to the color texture, to get points in the correct color. But you'll notice the results don't quite match up.

![]({{ site.baseurl }}/assets/images/rs-pointsbad.png){:width="600px"}

The depth cameras we are using are actually made up of a couple of sensors: an infrared sensor to record depth and a color sensor to record... color. These are two different components, each with their own lens, resolution, and field of view. Because of this, pixels at the same coordinates will most likely not match. There needs to be some type of formula to convert from depth space to color space and vice-versa. Moreover, the world positions are also in their own space. Pixel coordinate `(120, 12)` doesn't mean anything in 3D, so there needs to be another formula to convert from depth space to world space, and from color space to world space.

Luckily for us, depth cameras have all this already figured out and provide this information through their SDK. 

This can have many names, but it's usually called *registration*, *alignment*, or *correspondence*. 

There are a few options in how this is delivered.

* Secondary textures where the pixels are transformed to make a 1-1 relationship. For example, `ofxKinect` has a [`setRegistration()`](https://openframeworks.cc/documentation/ofxKinect/ofxKinect/#!show_setRegistration) function which outputs the color data in the same space as the depth data.
* Look-up tables (LUTs) (usually as textures) where you can calculate a pixel's world position based on the pixel value in the LUT. For example, [`ofxKinectForWindows2`](https://github.com/elliotwoods/ofxKinectForWindows2) uses an RGB float texture can be used to represent a vertex XYZ position.
* A getter function to map a pixel coordinate to a world position. For example, [`ofxKinectV2`](https://github.com/ofTheo/ofxKinectV2) has a `getWorldCoordinateAt(x, y)` function that does exactly that.
* A pre-mapped mesh that already has the depth and color mapped to world positions and provides the output data. For example, [`ofxRealSense2`](https://github.com/prisonerjohn/ofxRealSense2) has a `getPointsMesh()` function that can be drawn directly.

You'll have to read the documentation or look at the examples for the depth camera you are using to determine how to get correspondence between depth, color, and world space.

#### Intel RealSense

For our app, we can set the `ofxRealSense2::Device::alignMode` parameter to `Align::Color` to align the depth and color frames.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-aligned.cpp %}

Note that this still doesn't place our points in world space but at least they are colored accurately. To do this, we can use the `ofxRealSense2::Device::getWorldPosition(x, y)` function.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-worldPos.cpp %}

#### Microsoft Kinect

For `ofxKinect`, we just need to call `setRegistration()` to have the depth and color images correspond.

Here is the extruded texture example.

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-kinect.h %}
{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-kinectExtrude.cpp %}

And here is the example adjusted for world positions, using [`ofxKinect.getWorldCooordinateAt()`](https://openframeworks.cc//documentation/ofxKinect/ofxKinect/#!show_getWorldCoordinateAt).

{% gist c0a4c3b5858a4237aa870aa96bdc0f1f ofApp-kinectWorld.cpp %}

<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/89680830?color=ffffff&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
<p><a href="https://vimeo.com/89680830">CLOUDS - Overview</a> from <a href="https://vimeo.com/deepspeed">Jonathan Minard</a> on <a href="https://vimeo.com">Vimeo</a>.</p>