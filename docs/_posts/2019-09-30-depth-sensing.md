---
layout: post
title: "Depth Sensing"
categories: notes
---

So far, everything we have done has used video as input. Video is two-dimensional, it has a width and a height. 

Depth sensors are special types of cameras that add a third dimension to the mix; they can see width, height, and *depth*. Depth in this context means the distance away from the camera where a captured pixel is situated. 

It is important to note the difference between full 3D and depth. Just like a regular camera, a depth sensor can only capture what it "sees". If an object is obstructing another one behind it, the camera will only be able to measure the depth of the one closest to it. We can only have one depth measurement per pixel.

<div style="width:600px;margin:0 auto;" markdown="1">
[![Metal Pin Art Pin Point Impression 3D Frame Toy]({{ site.baseurl }}/assets/images/pin-art.jpg)
*Metal Pin Art Pin Point Impression 3D Frame Toy*](https://www.fasttech.com/product/1162800-metal-pin-art-pin-point-impression-3d-frame-toy)
</div>

<div style="width:600px;height:400px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/86175057?color=ffffff&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/86175057">MegaFaces: Kinetic Facade Shows Giant 3D &#039;Selfies&#039;</a> from <a href="https://vimeo.com/iart">iart</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

### Technologies

There are a few different technologies used for depth sensing, each with their own pros and cons depending on the application.

#### Coded (Structured) Light

[Coded (or structured) light](https://en.wikipedia.org/wiki/Structured-light_3D_scanner) works by projecting a known pattern onto the world, then analyzing the deformations of the pattern to determine the shapes of the surfaces it is projected on.

<div style="width:600px;margin:0 auto;" markdown="1">
<iframe width="560" height="315" src="https://www.youtube.com/embed/PluL7WTlKrM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

* Pros:
    * Precise.
    * High resolution, usually as high as the camera resolution it is using.
* Cons:
    * Can be slow as it needs multiple patterns projected then captured to generate a single frame. 
    * Subject to interference from outside light, so usually better for indoor sensing.

#### Time of Flight

Like the name implies, [time of flight](https://en.wikipedia.org/wiki/Time-of-flight_camera) works by measuring the time it takes for a beam of light to do a round-trip to the sensor. This involves complex calculations using the speed of light, and the shorter the travel time, the nearer the object. 

<div style="width:600px;margin:0 auto;" markdown="1">
[![Basler Time-of-Flight](https://www.baslerweb.com/fp-1496315461/media/en/editorial/content_images/images/Tof_Camera_Principle_EN_1380x735px_612x_.jpg)
*Basler Time-of-Flight*](https://www.baslerweb.com/en/products/cameras/3d-cameras/time-of-flight-camera/tof640-20gm_850nm/)
</div>

* Pros:
    * Compact. (This is the technology used in many current generation phones)
    * Fast, and ideal for real-time processing.
* Cons:
    * Mid-level accuracy.
    * Low resolution as it uses a custom sensor. 
    * Subject to interference from other devices.

#### Stereo Vision

Stereo vision works like human depth perception, where two cameras (or eyes) are placed side by side looking in the same direction. Differences in the two captured images, called *disparity*, is used to determine how far from the sensors these different pixels are. 

<div style="width:600px;margin:0 auto;" markdown="1">
[![Semi-global Matching Method](https://upload.wikimedia.org/wikipedia/commons/f/fd/Sgm_method.PNG)
*Semi-global Matching Method*](https://commons.wikimedia.org/wiki/File:Sgm_method.PNG)
</div>

* Pros:
    * Tends to be cheap, as any off the shelf cameras can be used.
    * Image representation is intuitive to humans.
* Cons:
    * Low accuracy.
    * Complex implementation requiring feature extraction and matching.

### Devices

Kinect
* Kinect
* Kinect V2
* Azure Kinect

Orbbec Astra

Intel RealSense 
* Historical 200 series
* 400 series

ZED

LeapMotion

For each:
* Technology
* IR or Color
* Resolution
* Range
* Platforms (Mac, Win, Linux?)

### Background subtraction example

### Mapping example

* Packing data into images
* Start thinking of Point Clouds and 3D points

