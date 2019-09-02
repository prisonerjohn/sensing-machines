---
layout: post
title: "Computer Vision"
categories: notes
---

Computer vision allows computers to "see", and to understand what they are seeing. This is done by reading and interpreting digital images and video.

<details>
    <summary>What are some examples of computer vision you can think of?</summary>
    <p class="border">
        <ul>
            <li>Image filtering.</li>
            <li>Background subtraction.</li>
            <li>Object recognition (contour detection, blob detection).</li>
            <li>Object tracking.</li>
            <li>Face detection (feature recognition, smile detection, etc.)</li>
            <li>Motion estimation (optical flow, motion vectors, etc.)</li>
            <li>Camera and projector calibration.</li>
        </ul>
    </p>
</details>

### Image Segmentation

One of the most common operations we will have to perform when working with computer vision is image segmentation. Image segmentation simply means dividing up the image pixels into meaningful groups. These meaningful groups depend on the application we are creating. For example, we may want to only consider the brightest pixels in an image, pixels of a certain color, clusters of pixels of a specific size, etc.

Image segmentation is the first step into many applications as it is a way to discard unwanted data and only keep what we need to focus on.

<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/8525186" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/8525186">night lights</a> from <a href="https://vimeo.com/thesystemis">zach lieberman</a> on <a href="https://vimeo.com">Vimeo</a>.*

### Video Capture

Let's begin with a simple app to stream data from a connected webcam.

We will use an [`ofVideoGrabber`](https://openframeworks.cc/documentation/video/ofVideoGrabber/) to capture frames from video.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-grabber.h %} 
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-grabber.cpp %}

We will use an [`ofImage`](https://openframeworks.cc/documentation/graphics/ofImage/) to store our thresholded image. Let's start with a stub procedure that just copies the video pixels into the image one at a time.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-image.h %} 
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-image.cpp %}

<details>
    <summary>Why is this not working?</summary>
    <p markdown="1">
        An `ofImage` is made up of two parts: [`ofPixels`](https://openframeworks.cc/documentation/graphics/ofPixels/), which is the data component of the image, and [`ofTexture`](https://openframeworks.cc/documentation/gl/ofTexture/), which is the graphics component of the image. For an image to get drawn to the screen, the data in `ofPixels` must be copied over to the `ofTexture`. When manipulating pixels directly as we are doing, this process needs to be triggered manually by calling [`ofImage.update()`](https://openframeworks.cc/documentation/graphics/ofImage/#show_update). 
    </p>
</details>

### Pass by reference vs. Pass by value?

You'll notice that this still does not appear to be working even after adding the call to `ofImage.update()`.

In C++, there are a few ways to pass data between objects and functions. You can pass data by reference, by value, or by pointer.
* Pass by reference means that we are passing the actual data object itself. Any changes we make to the received object will be kept in the original reference, as it is the same object.
* Pass by value means that we are just passing the value of the data, not the data object itself. This usually means making a copy of the original object and passing that copy. This does not make a difference when the data is a number (like an `int` or a `float`) but it does matter when the data is an object as any changes we make to the received object are only occurring on this new copied object.
* Pass by pointer means that we are passing the memory address of the data object. We will look at this later on in the course.

By default, data is passed by value in C++.

In our app, the line `ofPixels resultPix = resultImg.getPixels();` creates a copy of the image pixels and stores it in `resultPix`. Any changes we make to `resultPix` are changes made on the copy and not on the `ofPixels` belonging to `resultImg`.

One way to resolve this would be to save back the modified pixels to `resultImg` at the end of the loop:

```cpp
resultImg.setFromPixels(resultPix);
```

This, however, is highly unoptimized as we are now making two additional copies of our pixel array.

A better approach is to pass the original pixels by reference using the `&` operator, and not making any copies as we manipulate the data. We can even go ahead and pass the grabber pixels by reference and avoid making a copy there too.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-passRef.cpp %}

### Thresholding

Thresholding is a simple segmentation technique where a pixel value is either on or off. If it is on we will color it white and if it is off we will color it black. Thresholded images can be used as masks into our input image, used to discard any pixels we want to ignore.

Let's write a simple thresholding algorithm that only keeps the brightest parts of an image. We will use the brightness of each pixel color to determine if it should be on or off in our result image.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-threshold.cpp %}

We should make our threshold value editable, as we do not know what environment this app will run in. We will achieve this using `ofParameter` and the `ofxGui` addon. 
* [`ofParameter`](https://openframeworks.cc/documentation/types/ofParameter/) is a wrapper around a data object that gives it special abilities, like sending a notification whenever its value is changed. This makes is especially useful for GUIs where we need to act whenever a value is changed by clicking a button, moving a slider, etc. `ofParameter` objects use the template syntax, meaning that whatever type they hold is set between the `<>` symbols. In our case, since the threshold is an `int`, our `ofParameter` will be defined using `ofParameter<int>`.
* `ofxGui` is an addon that ships with OF for creating GUI elements. Because this is an addon, we will need to select it when generating our project, so that OF knows where to find the additional files for it. While we can use `ofxGui` without `ofParameter`, they complement each other well and result in simpler code to read.

![]({{ site.baseurl }}/assets/images/of-pg-gui.png){:width="360px"}

Our code now looks like the following, and our app window has a slider in the top-left corner we can use to edit the threshold value.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-guiParam.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-guiParam.cpp %}

### Background Subtraction

Background subtraction is a segmentation technique where the background pixels of an image are removed, leaving only the foreground data for processing. 

<details>
    <summary>What defines the background?</summary>
    <p>This can greatly vary depending on the type of sensor used, the environment, and the application. When using a depth sensor, we can actually use a pixel's distance from the camera to determine if it's in the background or not. In general for 2D video, a background pixel is one that is considered stable, i.e. that does not change it's value much or at all.</p>
</details>

Background subtraction requires that we have a background frame as a reference. We can do this by saving a video frame in memory, and comparing future frames to it in our update loop.

Video pixel values change slightly over time, so we cannot expect them to be identical frame by frame. We will add a threshold value and compare the difference between the pixels to determine if it should be on or off.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgRgb.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgRgb.cpp %}

### Color Space

We often get better results when comparing colors in HSV rather than RGB space. 
* RGB defines how much red, green, and blue is in an image. 
  * A small change in values might result in a greater difference seen, and vice-versa. 
  * When working in the grayscale range (white to black), it is hard to evaluate how much red, green, and blue is actually in the pixel.
* HSV defines colors as levels of hue, saturation, and brightness. 
  * This is closer to how humans perceive color, and differentiate objects they see in space.
  * It is easier to isolate parameters. We will often just care about the brightness of an image, particularly in the grayscale range.
  * It is a more logical set of parameters for many image analysis algorithms.

<video src="{{ site.baseurl }}/assets/videos/ofxcv-lights.mov" controls width="360px"></video>
<p markdown="1" class="caption">*[ofxCv](https://github.com/kylemcdonald/ofxCv) demo lights video*</p>

We will modify our app to use HSV, and allow tuning each parameter's threshold separately.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgHsv.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgHsv.cpp %}

### Devices

#### Color

While using built-in webcams is convenient for testing, it is not a great choice for deployed projects. They tend to be low quality and not offer manual controls for white balance, exposure, focus, etc.
* Higher end webcams like the [Logitech C920](https://www.logitech.com/en-us/product/hd-pro-webcam-c920s?crid=34) are a good alternative.
* For best quality, a DSLR or video camera can be used with a capture card, like the [Blackmagic UltraStudio Mini Recorder](https://www.blackmagicdesign.com/products/ultrastudio).

#### Infrared

Depending on the application and environment, it might be better to use an alternative to a color camera for capturing images.

Infrared cameras are often used for sensing because they sense light that is invisible to humans. They tend to be a more versatile choice as they can be used in a bright room, a pitch dark room, facing a video projector, behind a touch surface, etc.

<div style="padding:75% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/283105" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/283105">presence [a.k.a soft &amp; silky]</a> from <a href="https://vimeo.com/smallfly">smallfly</a> on <a href="https://vimeo.com">Vimeo</a>.*

Infrared USB cameras can be hard to come by. 
* One option is to get a depth sensor like an [Intel RealSense](https://www.intelrealsense.com/). Most of these also include an IR light emitter, which means it will always have enough light to work properly.
* Another popular option is to "hack" regular color cameras by adding a piece of processed film in front of the lens (which acts as an IR filter). There are a few [tutorials](https://www.instructables.com/id/Infrared-IR-Webcam/) on Instructables for doing this. A popular device for this hack is the [PS3 Eye](http://wiki.lofarolabs.com/index.php/Removing_the_IR_Filter_from_the_PS3_Eye_Camera) camera.
* Finally, you can opt for [security cameras](https://duckduckgo.com/?q=security+camera&t=ffab&iax=images&ia=images). These tend to have emitters around the lens to ensure there is enough light for the sensor. However, the quality is not great and they usually do not have USB connectivity and will require an adapter.

Thermographic cameras, commonly known as FLIR, can be an interesting option. These infrared cameras sense radiation/heat and represent it as a color map. This can be very useful for tracking humans or animals as they can easily be segmented from their surroundings.

[![FLIR Scout TK](https://cdn.vox-cdn.com/thumbor/exFqfuIsk_Lq64eP97pyHTrPON0=/800x0/filters:no_upscale()/cdn.vox-cdn.com/uploads/chorus_asset/file/5882635/TK_Dog.0.JPG){:width="360px" text-align:"center"}](https://www.theverge.com/2016/1/6/10727126/flir-scout-tk-hands-on-video-ces-2016)
