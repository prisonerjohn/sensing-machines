---
layout: post
title: "Mobile Development"
categories: notes
---

One of the strengths of openFrameworks is that it runs on a multitude of platforms. We can usually run our apps on Android or iOS with minimal modifications.

### iOS

OF runs on most iOS platforms, including iPhones, iPads, and Apple TVs.

#### Requirements

* You need a Mac and Xcode to compile apps for iOS.
* Xcode does not need any additional modules, it should already include all necessary libraries and applications.
* An Apple ID. You used to need a $99 Apple Developer account to compile apps on devices, but this limitation has been lifted as of Xcode 7.
* The iOS version of openFrameworks. The following code has been tested with the nightly [of_v20191109_ios_nightly.zip](https://openframeworks.cc/ci_server/versions/nightly/of_v20191109_ios_nightly.zip).

OF for iOS includes a variety of useful examples in the `/path/to/OF/examples/ios/` directory. As with everything we've seen so far, the examples are a great reference to get up and running quickly.

#### C++ on iOS

iOS apps were originally written in a language called Objective-C. Over the past few years, Apple has been pushing a modern language called Swift, but Objective-C is still widely supported and is often what runs under the hood of some frameworks.

Objective-C, like C++, is a superset of C and this makes it very easy to compile C++ for iOS. In fact, you can write C, Objective-C, and C++ code in the same classes and the compiler will usually be able to interpret the code correctly.

OF for iOS leverages this compiler to allow writing C++ code on top of an Objective-C layer used for window management and communication with the OS.

#### OF on iOS

OF for iOS is a set of extra files packaged as the `ofxiOS` addon. This addon does not need to be added using the Project Generator, it is automatically included for all iOS projects.

One of the first things you may notice when creating a new iOS OF app is that the file extensions for `main` and `ofApp` have changed from `cpp` to `mm`. `mm` is a special extension that tells the compiler that the code in the file is a mix of both C++ and Objective-C, or that the classes in the file interface with Objective-C objects. 

If the classes you create stay in the OF ecosystem, they can usually keep their `cpp` extension.

`main.mm` also uses a different object to set up the window, and includes much more options.

{% gist 82bd085256c16fc932e3eef6236a7d35 main-ios.mm %}

A couple of notable settings here:

* `enableHardwareOrientation` will allow the app to reorient itself as the device is rotated.
* `enableMultiTouch` will enable multitouch input, with fingers being tracked across frames.

The `ofApp` itself will have a few new callback functions available.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-iosBasic.h %}

* Mouse events are replaced by touch events like `touchDown()`, `touchMoved()`, and `touchDoubleTap()`. These use an [`ofTouchEventArgs`](https://openframeworks.cc/documentation/events/ofTouchEventArgs/#!show_ofTouchEventArgs) object as an argument, which holds touch pointer information like pressure, acceleration, and speed.
* `lostFocus()`  and `gotFocus()` are called when an application is moved to the background or the foreground.
* `deviceOrientationChanged()` is called when the device orientation changes.

#### Bouncing Balls

Let's port our bouncing balls example to iOS. 

* The `ezBall` class can be used as-is.
* The mouse and keyboard events are converted to use touches instead.
* We will use actual gravity to apply as a force to the balls! We will do this using the `ofxiOSCoreMotion` addon for OF. As this is already part of `ofxiOS`, we just need to include the header and are good to go.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-iosBalls.h %}
{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-iosBalls.mm %}

### Android

OF also runs on most Android platforms, including phones, tablets, and microcontrollers.

#### Requirements

* You need [Android Studio](https://developer.android.com/studio/) to compile apps for Android. This works on Mac, Windows, or Linux platforms. 
* You will also need to download the Android NDK (Native Development Kit) version r15c. Links for each platform are provided on the [OF Setup Guide](https://openframeworks.cc/setup/android-studio/).
* An Apple ID. You used to need a $99 Apple Developer account to compile apps on devices, but this limitation has been lifted as of Xcode 7.
* The Android version of openFrameworks. The following code has been tested with the [10.1 release](https://openframeworks.cc/versions/v0.10.1/of_v0.10.1_android_release.tar.gz).

OF for Android includes a variety of useful examples in the `/path/to/OF/examples/android/` directory. As with everything we've seen so far, the examples are a great reference to get up and running quickly.

The process here is unfortunately not as straightforward as iOS. Android Studio sometimes has trouble finding required header files, and will not auto-complete correctly or even mark errors in the code even if it compiles the app successfully. 

While there are other ways to compile apps for Android, Android Studio is the easiest as it takes care of downloading all required dependencies. I would suggest a workflow where code can be written in Xcode or Visual Studio then brought over to Android Studio when it's ready to be tested on a device.

#### C++ on Android

Android development is usually done in Java, but there have also always been bindings in C++ available through the [NDK](https://developer.android.com/ndk). This is how OF compiles apps for the Android platform. 

OF uses the [Gradle](https://gradle.org/) build system to compile apps for Android. Using Gradle decouples building from the IDE, and allows making customizations in the build system without having to go through convoluted menus or being dependent on a specific program. While this has many advantages, it's also the reason why Android Studio will have trouble finding dependencies even if the app is building successfully.

#### OF on Android

OF for Android is a set of extra files packaged as the `ofxAndroid` addon. This addon does not need to be added using the Project Generator, it is automatically included for all Android projects.

Android uses a concept called ["Activities"](https://developer.android.com/guide/components/activities/intro-activities) to organize applications into modules. OF is basically an Android Activity that runs in an app.

`main.cpp` will have an extra section at the bottom to trigger a call to the `main()` function from the OF activity. This is encapsulated in an `#ifdef` compiler directive so that the same file can be used for non-Android apps, and that section will just be ignored.

{% gist 82bd085256c16fc932e3eef6236a7d35 main-android.cpp %}

The `ofApp` itself will have a few new callback functions available.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidBasic.h %}

* Mouse events are replaced by touch events like `touchDown()`, `touchMoved()`, and `touchDoubleTap()`.
* `pause()`  and `resume()` are called when an application is moved to the background or the foreground. `stop()` is called when an application quits.

#### Bouncing Balls

Let's port our bouncing balls example to Android. 

* The `ezBall` class can be used as-is.
* The mouse and keyboard events are converted to use touches instead.
* We will use actual gravity to apply as a force to the balls! We will do this using the `ofxAccelerometer` addon for OF. As this is already part of `ofxAndroid`, we just need to include the header and are good to go.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidBalls.h %}
{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidBalls.cpp %}

<div style="width:600px;height:400px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/122138826?color=ef0065&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/122138826">Learning Lab: Mars Base</a> from <a href="https://vimeo.com/scatterco">Scatter</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

<div style="width:600px;height:400px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/122138852?color=ef0065&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/122138852">Fitzania</a> from <a href="https://vimeo.com/scatterco">Scatter</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

### Computer Vision

Things get interesting when we start doing more advanced processing on mobile devices. Let's try to do some computer vision directly on our phones.

#### Video

The OF core tries to have parity across all platforms, as such we just need to create an `ofVideoGrabber` as we have been doing so far to get access to the device's camera.

Here is an example of this on Android.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidGrabber.h %}
{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidGrabber.cpp %}

Note that we can also use the device list to pick between front or back camera! Have a look at the relevant apps in the iOS or Android examples for the code to do so.

#### OpenCV

Addons can also be used on mobile platforms, as long as they include all necessary files for the target platforms.

* If an addon consists of sources only, there is a good chance it will run on mobile as the sources will be compiled to the target platform in the build process.
* If an addon includes pre-compiled libraries, these need to include the correct variants to work on mobile platforms.

OpenCV can run almost anywhere, and OF includes the precompiled libraries for both iOS and Android. Let's run a simple thresholding example that uses `ofxCv`. In both cases, we can use the Project Generator to set up our project including all prerequisites.

The following is the version for Android.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidThreshold.h %}
{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-androidThreshold.cpp %}

<div style="width:600px;margin:0 auto;" markdown="1">
<video src="{{ site.baseurl }}/assets/videos/android-cv.mp4" controls width="100%"></video>
</div>

And the following is the version for iOS.

{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-iosThreshold.h %}
{% gist 82bd085256c16fc932e3eef6236a7d35 ofApp-iosThreshold.mm %}
