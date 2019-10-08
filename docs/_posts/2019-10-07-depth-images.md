---
layout: post
title: "Depth Images"
categories: notes
---

### Depth Grabbers

While depth sensors come in a variety of shapes, sizes, and technologies, the method to interface with them is usually pretty similar. Just like color cameras, depth cameras deliver images at the requested framerate. These images come in as arrays of pixels, which can then be manipulated and uploaded to a texture for rendering.

As these are specialty devices, we cannot use an `ofVideoGrabber` to retrieve data from them; we'll have to use a special "grabber" tailored to each device and its SDK. 

<details>
	<summary>How come <code>ofVideoGrabber</code> works for all webcams?</summary>
	<p markdown="1">
	[USB Video Class](https://en.m.wikipedia.org/wiki/USB_video_device_class), or UVC, is a standard describing formats by which a device can stream video frames. Most USB webcams follow this standard and that is why we can use a common interface to access their streams. 
	</p>
	<p markdown="1">
	`ofVideoGrabber` is then able to work with any USB video device that complies with the UVC standard. It is actually a wrapper with specific implementations based on the type of system we are on, for example [AVFoundation](https://developer.apple.com/av-foundation/) on macOS, [GStreamer](https://gstreamer.freedesktop.org/) on Linux, and [Windows Media Foundation](https://docs.microsoft.com/en-us/windows/win32/medfound/microsoft-media-foundation-sdk) on Windows.
	</p>
</details>

We fortunately will not have to implement this ourselves. In the same way that `ofxCv` acts as a bridge between OpenCV and openFrameworks, there are many [OF addons](http://ofxaddons.com/categories) that have already taken care of managing the interface between the sensor SDK and openFrameworks. 

#### Intel RealSense

[`ofxRealSense2`](https://github.com/prisonerjohn/ofxRealSense2) is a good choice for the Intel RealSense, as it gives us both pixel and texture access, as well as control over many of the SDK's filtering options. The repo download doesn't always include the lib files, so your best bet is to download it from the [Releases](https://github.com/prisonerjohn/ofxRealSense2/releases) page.

{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-basic.h %}
{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-basic.cpp %}

A depth image will usually be single-channel, and therefore rendered in grayscale. Each gray value represents the distance of that pixel to the camera. The convention is usually brighter for nearer objects, but this is just representative.

![]({{ site.baseurl }}/assets/images/rs-depth.png){:width="600px"}

Let's read the actual depth value using the SDK function `ofxRealSense2::Device.getDistance(x, y)`. We will read the value under the mouse, and display it using [`ofDrawBitmapStringHighlight()`](https://openframeworks.cc/documentation/graphics/ofGraphics/#!show_ofDrawBitmapString) for debugging.

{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-distance.cpp %}

Note the use of [`ofClamp()`](https://openframeworks.cc/documentation/math/ofMath/#!show_ofClamp). This ensures our sampling coordinate is always within the bounds of the depth pixel array.

This value probably comes from the depth texture. Let's try to read it directly from the pixels array and see if the values match. Note that there are usually two available depth readings:

* The raw depth buffer contains the actual depth measurement. 
  * The value can be read directly from the pixel value.
  * The value is metric, usually in millimeters (1mm = 0.001m).
  * The pixel format is `unsigned short`, with range `0` to `65535` (16-bit).
* The depth buffer contains a scaled representation of the pixel data.
  * This is just for us to make sure everything is working, as the raw depth image is usually very dark or very bright.
  * We should not use this for any depth readings.
  * The pixel format is `unsigned char`, with range `0` to `255` (8-bit). This is the same as most RGB images.
  * This is sometimes called the *scaled* or *mapped* image.

We will therefore read our value from the raw depth texture.

{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-rawDepth.cpp %}

Note that we are getting an `ofColor` from the depth pixels, and then just reading the red channel with `ofColor.r` to get the actual value. We could use any of the red, green, blue channels here; as our data is in a single grayscale channel, all the colors represent the same value.

#### Microsoft Kinect

[`ofxKinect`](https://openframeworks.cc/documentation/ofxKinect/) is the best choice for the original Microsoft Kinect, as it ships with OF and gives us all the data we need.

Notice that the code looks almost similar to what we just did for the RealSense!

{% gist 42cbc92be72fbf70b7b498d41e06e686 ofApp-oneDepth.h %}
{% gist 42cbc92be72fbf70b7b498d41e06e686 ofApp-oneDepth.cpp %}

#### Microsoft Kinect V2

[`ofxKinectForWindows2`](https://github.com/elliotwoods/ofxKinectForWindows2) is a good choice for the Kinect V2. It works with the [Microsoft Kinect for Windows 2.0 SDK], which means it supports all Kinect features (including body tracking), but only works on Windows.

Note that `ofxKinectForwindows2` doesn't work out of the box with the Project Generator! You will need to make a modification to the project properties in Visual Studio as follows:

![]({{ site.baseurl }}/assets/images/vs-kfw2.png){:width="600px"}

Alternatively, [`ofxKinectV2`](https://github.com/ofTheo/ofxKinectV2) is a cross-platform solution that works similarly to `ofxKinect`.

`ofxKinectForWindows2` does not include a function to get distance from a coordinate, so we will need to sample the depth texture directly.

{% gist 42cbc92be72fbf70b7b498d41e06e686 ofApp-twoDepth.h %}
{% gist 42cbc92be72fbf70b7b498d41e06e686 ofApp-twoDepth.cpp %}

### Depth Threshold

Depth pixels are very useful for thresholding images. This tends to be much more precise than using brightness or color (as we have been doing previously) since we eliminate any issues with change in lighting or with similarities between background and foreground colors. We can set a depth range that valid pixels belong to and discard anything that's nearer or farther than this range.

Let's first attempt to do this manually by iterating through the pixel array.

{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-threshold.h %}
{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-threshold.cpp %}

![]({{ site.baseurl }}/assets/images/rs-threshold.png){:width="600px"}

We can also achieve the same effect using OpenCV with two consecutive calls to [`cv::threshold()`](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html?highlight=threshold#threshold).
* The first will be for the near value, and will keep everything greater than the threshold value.
* The second will be for the far value, and will be inverted so that we keep everything smaller than the threshold value.
* We then combine the result of both operations using [`cv::bitwise_and()`](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html?highlight=bitwise_and#bitwise-and), which just adds both textures together.

Unfortunately, OpenCV does not work with `unsigned short` images, so we cannot use our array of depth pixels directly. We first need to convert it either to an array of `unsigned char` or `float`. 
* If we go with `unsigned char`, we will lose precision because we will need to pack `65536` possible values into `256`. We should therefore use `float` and `ofFloatPixels`.
* In both cases, if we let OF do the conversion automatically, it will rescale the values to fit into the new range. So `[0, 65535]` becomes `[0, 255]` or `[0.0, 1.0]`. We need to remain aware of this as it will change the range of our near/far parameters.

Here is a second thresholding attempt using OpenCV.

{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-cv.h %}
{% gist 2b564edede9ff89de8bf495397a19a79 ofApp-cv.cpp %}

And here is that same example using a Kinect and 
