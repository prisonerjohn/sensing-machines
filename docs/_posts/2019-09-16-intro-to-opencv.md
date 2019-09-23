---
layout: post
title: "Intro to OpenCV"
categories: notes
---

[OpenCV](https://opencv.org/) is an open-source library for performing computer vision operations.
* OpenCV was originally released in 2000 and has gone through many updates and revisions since then.
* The library is cross-platform and available for all major platforms. It includes interfaces in C++, Java, and Python.
* OpenCV includes hundreds of algorithms, for performing a variety of tasks like image conversion, object tracking, feature recognition, camera calibration, etc.

OpenCV uses its own image type, called [`cv::Mat`](https://docs.opencv.org/4.1.1/d3/d63/classcv_1_1Mat.html). This is similar to `ofPixels`, as it holds an array of pixel values, but it's much more powerful as you can perform all types of operations on them. For example, you can add or multiply two `cv::Mat` objects directly, without needing to loop through the pixels one at a time.

<details>
    <summary>What does the <code>::</code> mean here?</summary>
    <p markdown="1">
    We have already covered that `::` is a scope resolution operator in C++. We first encountered `::` to show the relationship between methods and classes, and here it is used to show the relationship between classes and namespaces. 
    </p>
    <p markdown="1">
    A [namespace](https://en.cppreference.com/w/cpp/language/namespace) is a top-level group that holds classes that are related to each other. It is similar to a Java package. For example, `cv::Mat` is a reference to the `Mat` class that belongs to the `cv` namespace.
    </p>
    <p markdown="1">
        Classes in the same namespace can refer to each other directly, but classes outside of that namespace need to specify the namespace using the `::` notation to refer to its classes.
    </p>
    <p markdown="1">Most classes in OF do not belong to a namespace, which is why we have not seen this yet, but this is gradually changing. New additions to the framework, like the [`glm`](https://glm.g-truc.net/0.9.9/index.html) math library are keeping their namespace visible, so we will encounter types like `glm::vec2` and `glm::vec3` when manipulating vectors.
    </p>
</details>

### OpenCV for openFrameworks

While you can use the OpenCV library directly in OF (since both are written in C++), this would require image type conversions every time we would need to transfer from one framework to the other. For example, to convert our background subtraction algorithm we would:
1. Capture a frame from the camera in "OF space", using `ofVideoGrabber` and `ofPixels`.
2. Convert the `ofPixels` to a `cv::Mat` to use the pixels in "OpenCV space".
3. Perform the background subtraction using OpenCV functions.
4. Convert the result `cv::Mat` back to an `ofImage` to draw it to the screen.

This also applies to other types which we will use. For example:
* Points ([`cv::Point`](https://docs.opencv.org/4.1.1/db/d4e/classcv_1_1Point__.html) vs [`glm::vec2`](https://openframeworks.cc/documentation/glm/glm%3A%3Avec2/))
* Rectangles ([`cv::Rect`](https://docs.opencv.org/4.1.1/d2/d44/classcv_1_1Rect__.html) vs [`ofRectangle`](https://openframeworks.cc/documentation/types/ofRectangle/))

We will use an openFrameworks addon to interface with OpenCV. This will take care of handling these conversions and give us additional OF-specific methods we can use.

There are two options for addons:
* [`ofxOpenCv`](https://openframeworks.cc/documentation/ofxOpenCv/) is the built-in OpenCV addon. This wrapper hides most of the native OpenCV structures and methods and allows us to work strictly in "OF space". While this simplifies our work, we are limited by the data types and algorithms that are included in the addon.
* [`ofxCv`](https://github.com/kylemcdonald/ofxCv) is a user-contributed addon that takes a more transparent approach. Interchange between OF and CV is facilitated with helper functions, but the majority of the work is done using native OpenCV calls. While this may seem more complicated, it reduces our dependency to OF as we are learning how to use OpenCV directly.

We will use `ofxCv` for this class. As it is a user-contributed addon, we will need to [download](https://github.com/kylemcdonald/ofxCv) it and unzip it in the OF addons directory at `/path/to/OF/addons/`. Once that is done, the Project Generator will automatically detect the addon and allow us to include it in our projects.

`ofxCv` uses the OpenCV files from the `ofxOpenCv` addon, so make sure to include both in your projects.

![]({{ site.baseurl }}/assets/images/of-pg-ofxcv.png){:width="360px"}

### OpenCV Background Subtraction

Let's write a new background subtraction example using OpenCV. We will focus on pixel brightness for our operations, so we will convert our images to grayscale before processing.

{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-bgGray.h %}
{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-bgGray.cpp %}

Note the use of the `ofxCv::absdiff()` and `ofxCv::threshold()` functions for processing all our pixels in a single line of code. These are wrappers for [`cv::absdiff()`](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html?highlight=absdiff#absdiff) and [`cv::threshold()`](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html?highlight=threshold#threshold) allowing us to use `ofImage` objects in `cv` space. `ofxCv` handles all the necessary conversions behind the scenes.

As our apps get more complex, we may want to skip these conversions to optimize our code and have it run faster. Let's rewrite the previous example using `cv` objects for our CV operations, i.e. replace `ofImage` with `cv::Mat`. 

We will also make two additional optimizations:
1. Cache the variables used for grabber images so that they don't get reallocated every frame. We do this by adding two new variables `grabberColorMat` and `grabberGrayMat` to the `ofApp` class.
1. Only run through our algorithms when a new video frame is captured. This is an important step as the video camera will usually run at much lower framerate than the application itself. We do this by checking [`ofVideoGrabber.isFrameNew()`](https://openframeworks.cc/documentation/video/ofVideoGrabber/#!show_isFrameNew) before running through our CV algorithm.

{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-bgCv.h %}
{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-bgCv.cpp %}

### Face Detection

OpenCV includes a powerful object detection algorithm which is often used for finding faces in images, using something called Haar cascade classifiers.

This is a machine learning algorithm where a classifier is trained on many sample images, both positive (with faces) and negative (without faces). It uses this to generate a model, which it can then use to detect faces in new images by looking for similar patterns.

The features are in the shape of black and white patterns (Haar features), which are searched for in an image. If the patterns are arranged in a recognizable way, we have a match!

<div style="width:600px;margin:0 auto;" markdown="1">
[![Haar Features](https://docs.opencv.org/3.4/haar_features.jpg){:width="100%" text-align:"center"}![Haar Features](https://docs.opencv.org/3.4/haar.png){:width="100%" text-align:"center"}
*Haar Features*](https://docs.opencv.org/3.4/db/d28/tutorial_cascade_classifier.html)
</div>

Let's have a look at the face example that ships with `ofxCv`. Note that it does not come with the Haar cascade file, you'll need to add it yourself to the `OF/addons/ofxCv/example-face/bin/data/` folder. You can get the face file in the `OF/examples/computer_vision/opencvHaarFinderExample/bin/data/` folder, or you can try some of the other ones from the [OpenCV repository](https://github.com/opencv/opencv/tree/master/data/haarcascades).

{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-face.h %}
{% gist 95ad94acc37baf167c4f2f15129f737b ofApp-face.cpp %}

<details>
    <summary>What does <code>using namespace</code> mean here?</summary>
    <p markdown="1">
    A [`using`](https://en.cppreference.com/w/cpp/language/namespace) directive can be used in C++ to indicate that classes and methods from the specified namespace will be used in the file.
    </p>
    <p markdown="1">
    When adding `using namespace ofxCv;` at the top of the file, you can use classes from `ofxCv` without having to prefix them with `ofxCv::`. This can be seen with `ObjectFinder::Fast` in the code above, which if fully defined is `ofxCv::ObjectFinder::Fast`.
    </p>
    <p markdown="1">
    Some people prefer `using` directives because it makes the code more concise. Others prefer being explicit and referring the namespace throughout. Both options are fine, it's a matter of style.
    </p>
</details>

<div style="width:600px;margin:0 auto;" markdown="1">
<iframe src="https://player.vimeo.com/video/96549043?title=0&byline=0&portrait=0" width="640" height="360" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>
<p markdown="1">*<a href="https://vimeo.com/96549043">Sharing Faces</a> from <a href="https://vimeo.com/kylemcdonald">Kyle McDonald</a> on <a href="https://vimeo.com">Vimeo</a>.*</p>
</div>
