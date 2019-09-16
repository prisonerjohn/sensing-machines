---
layout: post
title: "Computer Vision"
categories: notes
---

Computer vision allows computers to "see", and to understand what they are seeing. This is done by reading and interpreting digital images and video.

What are some common computer vision operations?

* Image filtering
  * Convert from one color space to another (e.g. RGB to grayscale).
  * Adjust brightness and contrast.
  * Blur or sharpen the image.
  * Edge detection.

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![Understanding Edge Detection (Sobel Operator)](https://miro.medium.com/fit/c/1838/551/1*4lPMjSPaS2JLWZAaYrXr2Q.jpeg)
    *Understanding Edge Detection (Sobel Operator)*](https://medium.com/datadriveninvestor/understanding-edge-detection-sobel-operator-2aada303b900)
    </div>

* Background subtraction
  * Detect moving objects by comparing them to a reference frame.

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![IHDC: Idiap Human Detection Code](https://www.idiap.ch/~odobez/human-detection/media/background-subtraction-1.png)
    *IHDC: Idiap Human Detection Code*](https://www.idiap.ch/~odobez/human-detection/index.html)
    </div>

* Object recognition
  * Blob detection
  * Contour finding

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![OpenCV Contours & Convex Hull 2 Structure plugin](http://kineme.net/files/composition/benoitlahoz/CarasueloContours.jpg)
    *OpenCV Contours & Convex Hull 2 Structure plugin*](http://kineme.net/taxonomy/term/114/0)
    </div>

* Motion estimation
  * Track pixel movement between consecutive frames and infer the direction objects are moving into.

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![Dense Realtime Optical Flow on the GPU](http://cs.brown.edu/courses/csci1290/2011/results/final/psastras/images/sequence0/save_0.png)
    *Dense Realtime Optical Flow on the GPU*](https://cs.brown.edu/courses/csci1290/2011/results/final/psastras/)
    </div>
  
* Face detection
  * Feature recognition
  * Smile detection

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![Object Detection : Face Detection using Haar Cascade Classifiers](https://www.bogotobogo.com/python/OpenCV_Python/images/FaceDetection/xfiles4.png){:width="100%" text-align:"center"}
    *Object Detection : Face Detection using Haar Cascade Classifiers*](https://www.bogotobogo.com/python/OpenCV_Python/python_opencv3_Image_Object_Detection_Face_Detection_Haar_Cascade_Classifiers.php)
    </div>

* Camera and projector calibration

    <div style="width:600px;margin:0 auto;" markdown="1">
    [![Procamcalib](http://projection-mapping.org/wp-content/uploads/2015/02/procamcalib-thumb.png){:width="100%" text-align:"center"}
    *Procamcalib*](http://projection-mapping.org/tools/procamcalib/)
    </div>

    <div style="width:600px;height:400px;margin:0 auto;" markdown="1">
    <iframe width="600" height="375" src="https://www.youtube.com/embed/lX6JcybgDFo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    *Box by Bot & Dolly.*
    </div>

### Image Segmentation

One of the most common operations we will have to perform when working with computer vision is image segmentation. Image segmentation simply means dividing up the image pixels into meaningful groups. These meaningful groups depend on the application we are creating. For example, we may want to only consider the brightest pixels in an image, pixels of a certain color, clusters of pixels of a specific size, etc.

<div style="width:600px;margin:0 auto;" markdown="1">
[![Shape-based hand recognition approach using the morphological pattern spectrum](https://www.researchgate.net/profile/Gabriel_Sanchez-Perez/publication/220050099/figure/fig1/AS:277473016205312@1443166130542/Hand-shape-segmentation-grayscale-captured-image-and-its-corresponding-binary-segmented.png){:width="100%" text-align:"center"}
*Shape-based hand recognition approach using the morphological pattern spectrum*](https://www.researchgate.net/publication/220050099_Shape-based_hand_recognition_approach_using_the_morphological_pattern_spectrum)
</div>


Image segmentation is the first step into many applications as it is a way to discard unwanted data and only keep what we need to focus on.

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

<div style="width:600px;height:400px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/8525186" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/8525186">night lights</a> from <a href="https://vimeo.com/thesystemis">zach lieberman</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

Let's write a simple thresholding algorithm that only keeps the brightest parts of an image. We will use the brightness of each pixel color to determine if it should be on or off in our result image.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-threshold.cpp %}

We should make our threshold value editable, as we do not know what environment this app will run in. 

Let's make `brightnessThreshold` a class variable by moving its declaration to the header file. We can then use the mouse position to adjust the value in every update loop. We will use the [`ofMap()`](https://openframeworks.cc/documentation/math/ofMath/#!show_ofMap) function to easily convert our mouse position (from 0 to the width of the window) to our brightness range (from `0` to `255`).

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-threshMouse.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-threshMouse.cpp %}

While this works, it is a little clunky. We want better control and feedback for our threshold value, so let's use a slider instead of the mouse to set the value.

We will achieve this using two new elements: the `ofxGui` addon and the `ofParameter` template class.

### ofxGui

The `ofxGui` addon ships with OF and is used for creating GUI elements. 

"Addon" means it is not part of the OF core files. We need additional files in our project to use it. This can be done by selecting it in the Project Generator.

![]({{ site.baseurl }}/assets/images/of-pg-gui.png){:width="360px"}

When we regenerate our project files, we will now have access to all the `ofxGui` classes. 

For this example, we will use [`ofxPanel`](https://openframeworks.cc/documentation/ofxGui/ofxPanel/), which is simply a container that can hold other GUI controls like buttons and sliders.

### ofParameter

[`ofParameter`](https://openframeworks.cc/documentation/types/ofParameter/) is a wrapper around a data object that gives it super powers. For example:
* Min and max values can be defined and the value will always stay within that range.
* A notification gets triggered whenever its value is changed. This is especially useful for GUIs where we need to respond right away when a variable changes.

`ofParameter` uses the template syntax, meaning that whatever type they hold is set between the `<>` symbols. In our case, since the threshold is an `int`, our `ofParameter` will be defined using `ofParameter<int>`.

<details>
	<summary>What is a template?</summary>
	<p markdown="1">
	C++ has a concept of [templates](https://en.cppreference.com/w/cpp/language/templates). The idea with templates is to pass types as parameters, similar to how we are used to pass values as parameters. This makes it so that we don't have to write the same code over and over for different data types.
	</p>
	<p markdown="1">
	You will usually see classes or functions be templated.
    </p>
   	<p markdown="1">
    In fact, we have already been using templates with `ofPixels`! The `ofPixels` type is actually a [shorthand](https://github.com/openframeworks/openFrameworks/blob/master/libs/openFrameworks/graphics/ofPixels.h#L661) for `ofPixels_<unsigned char>`. This means it is an [`ofPixels_`](https://openframeworks.cc/documentation/graphics/ofPixels/#!show_ofPixels_) template where the data type of the pixels is `unsigned char` (and that is why our values go from `0` to `255`).
    </p>
   	<p markdown="1">
    `ofPixels_` can also be used with `float` and `unsigned short` pixels, using `ofPixels_<float>` or `ofPixels_<unsigned short>`. The shorthands `ofFloatPixels` and `ofShortPixels` are also available, and they represent exactly the same thing.
	</p>
	<p markdown="1">
	Our `ofParameter` works in a similar way, it is a template class. We need to specify the type and precision of the data it will control, in this case `int`. This is done when declaring the variable with type `ofParameter<int>`.
	</p>
</details>

Our code now looks like the following, and our app window has a slider in the top-left corner we can use to edit the threshold value.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-guiParam.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-guiParam.cpp %}

### Background Subtraction

Background subtraction is a segmentation technique where the background pixels of an image are removed, leaving only the foreground data for processing. 

<details>
    <summary>What defines the background?</summary>
    <p>This varies depending on the type of sensor used, the environment, and the application. When using a depth sensor, we can actually use a pixel's distance from the camera to determine if it's in the background or not. In general for 2D video, a background pixel is one that is considered stable, i.e. that does not change its value much or at all.</p>
</details>

Background subtraction requires that we have a background frame as a reference. We can do this by saving a video frame in memory, and comparing future frames to it in our update loop.

Video pixel values change slightly over time, so we cannot expect them to be identical frame by frame. We will add a threshold value and compare the difference between the pixels to determine if it should be on or off.

{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgRgb.h %}
{% gist 249987b76ba66d33b5ecf1f2d9d904ad ofApp-bgRgb.cpp %}

### Devices

#### Color

While using built-in webcams is convenient for testing, it is not a great choice for deployed projects. They tend to be low quality and not offer manual controls for white balance, exposure, focus, etc.
* Higher end webcams like the [Logitech C920](https://www.logitech.com/en-us/product/hd-pro-webcam-c920s?crid=34) are a good alternative.
* For best quality, a DSLR or video camera can be used with a capture card, like the [Blackmagic UltraStudio Mini Recorder](https://www.blackmagicdesign.com/products/ultrastudio).

#### Infrared

Depending on the application and environment, it might be better to use an alternative to a color camera for capturing images.

Infrared cameras are often used for sensing because they sense light that is invisible to humans. They tend to be a more versatile choice as they can be used in a bright room, a pitch dark room, facing a video projector, behind a touch surface, etc.

<div style="width:600px;height:490px;margin:0 auto;" markdown="1">
<div style="padding:75% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/283105" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/283105">presence [a.k.a soft &amp; silky]</a> from <a href="https://vimeo.com/smallfly">smallfly</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

Infrared USB cameras can be hard to come by. 
* One option is to get a depth sensor like an [Intel RealSense](https://www.intelrealsense.com/). Most of these also include an IR light emitter, which means it will always have enough light to work properly.
* Another popular option is to "hack" regular color cameras by adding a piece of processed film in front of the lens (which acts as an IR filter). There are a few [tutorials](https://www.instructables.com/id/Infrared-IR-Webcam/) on Instructables for doing this. A popular device for this hack is the [PS3 Eye](http://wiki.lofarolabs.com/index.php/Removing_the_IR_Filter_from_the_PS3_Eye_Camera) camera.
* Finally, you can opt for [security cameras](https://duckduckgo.com/?q=security+camera&t=ffab&iax=images&ia=images). These tend to have emitters around the lens to ensure there is enough light for the sensor. However, the quality is not great and they usually do not have USB connectivity and will require an adapter.

Thermographic cameras, commonly known as FLIR, can be an interesting option. These infrared cameras sense radiation/heat and represent it as a color map. This can be very useful for tracking humans or animals as they can easily be segmented from their surroundings.

<div style="width:600px;height:400px;margin:0 auto;" markdown="1">
[![FLIR Scout TK](https://cdn.vox-cdn.com/thumbor/exFqfuIsk_Lq64eP97pyHTrPON0=/800x0/filters:no_upscale()/cdn.vox-cdn.com/uploads/chorus_asset/file/5882635/TK_Dog.0.JPG){:width="100%" text-align:"center"}](https://www.theverge.com/2016/1/6/10727126/flir-scout-tk-hands-on-video-ces-2016)
</div>