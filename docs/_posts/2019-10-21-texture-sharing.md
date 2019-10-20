---
layout: post
title: "Texture Sharing"
categories: notes
---

It is sometimes necessary to share more than just state updates or short messages between applications. You may need to share an entire image over for various reasons:

* Continue image processing in another app.
* Use OF image as a texture for a material in a 3D rendering application.
* Projection map the OF image using mapping software.
* Use OF as an input for VJ software.
* Etc.

Texture sharing is often required for building interactive installations, and there are a few options for doing so. The following frameworks use special optimized techniques to share images, and therefore tend to be much faster than something built for shorter messages, like OSC for example.

<div style="width:600px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/214896709?color=ffffff&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/214896709">Shattered Identity / Real Time Webcam Projection Mapping</a> from <a href="https://vimeo.com/ozzo">OZZO</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

### Syphon

[Syphon](http://syphon.v002.info/) is a macOS technology that allows applications to share frames in real-time. It does this by sharing texture memory avoiding unnecessary copies, and keeping data on the GPU as much as possible.

Syphon has plug-ins available for a multitude of platforms including [openFrameworks](https://github.com/astellato/ofxSyphon), [Processing](https://github.com/Syphon/Processing), [Unity](https://github.com/keijiro/KlakSyphon), [Jitter](https://github.com/Syphon/Jitter/releases/), [VDMX](https://vidvox.net/), [MadMapper](https://madmapper.com/), etc. It is an open-source project, which makes it easy for anyone to build their own bindings to Syphon, and this is why the list of supported applications keeps growing.

We will use the [ofxSyphon](https://github.com/astellato/ofxSyphon) addon for openFrameworks. This includes both server and client implementations, meaning we can send and receive images to / from any other application that supports Syphon.

#### Server

The following is a simple thresholding app that includes two servers: 
1. `serverGrabber` publishes the input video using the `ofxSyphonServer.publishTexture()` function. 
  * This takes a pointer to an `ofTexture` as an argument, so we will use the `&` operator before the variable to convert it to a pointer.
1. `serverThreshold` publishes the result threshold image using `ofxSyphonServer.publishScreen()`. 
  * `publishScreen()` sends whatever has been drawn on screen up to that point, so we need to draw `thresholdImg` before calling it.
  * Note that the GUI panel does not get sent to Syphon because it is drawn after the call to `publishScreen()`.

{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-server.h %} 
{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-server.cpp %}

#### Client

We can now write a client app that reads the published textures. 
* Note that each `ofxSyphonClient` needs to be set with a corresponding name to the `ofxSyphonServer`.
* We first need to call `ofxSyphonClient.setup()` then `ofxSyphonClient.set()` to set the server and app name. The server name is whatever we used in our sender ("Video Input" or "Threshold Image" above), and the app name is the name of the executable. In our case it would be something like "syphon-sendDebug".

{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-client.h %} 
{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-client.cpp %}

#### Server Directory

While this client works, it is not ideal that we have to know the server name ahead of time because this is prone to errors and the name might change at any time. `ofxSyphon` also comes with an `ofxSyphonServerDirectory` which lists all available servers on the machine.

Let's use this to write a more robust client application. We will use an event listener to automatically call a function when the value of an `ofParameter` is changed, and set the client using this info.

{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-dirOne.h %} 
{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-dirOne.cpp %}

#### Event Listeners

Event listeners in openFrameworks are functions that are automatically called when an event happens. We have used pre-configured listeners like `mousePressed()` before, but we can also set up our own.

An event listener function first needs to be defined. This is simply a function that takes a reference to a variable as an argument. When using listeners to respond to changes to `ofParameter`, the argument needs to be the same as the `ofParameter` type. 

For example, to listen for changes to an `ofParameter<int>`, our function argument would have to be a variable of type `int&`.

Once the function is defined, it needs to be registered with the object it is listening to.
* If we are dealing with an [`ofEvent`](https://openframeworks.cc///documentation/events/ofEvent/) directly, we can call [`ofEvent.add()`](https://openframeworks.cc/documentation/events/ofEvent/#show_add) or [`ofAddListener()`](https://openframeworks.cc/documentation/events/ofEventUtils/#!show_ofAddListener).
* If we are using an `ofParameter`, we call [`ofParameter.addListener()`](https://openframeworks.cc/documentation/types/ofParameter/#show_addListener).

In all cases, we need to pass a pointer to the object where the listener is defined, usually `this`, and a pointer to the listener function itself, usually something like `&ofApp:Listener`.

The following example adds listeners to the `serverAnnounced` and `serverRetired` events of `ofxSyphonServerDirectory` to ensure our index is always within range.

{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-dirTwo.h %} 
{% gist a6ae4e71afb6ddebf2afb50f62fc52b5 ofApp-dirTwo.cpp %}

<div style="width:600px;margin:0 auto;" markdown="1">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/129234412?color=ef0065&title=0&byline=0&portrait=0" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
*<a href="https://vimeo.com/129234412">C4D_OF_ImageProcessing_SyphonLink</a> from <a href="https://vimeo.com/adamheslop">Adam Heslop</a> on <a href="https://vimeo.com">Vimeo</a>.*
</div>

### Spout

[Spout](http://spout.zeal.co/) is the equivalent to Syphon but for Windows platforms. It uses a special GPU feature that allows textures to be shared between different apps running on the same PC, and has a fallback method in case the hardware does not support it.

Similarly to Syphon, Spout already has plug-ins available for a multitude of platforms, and being open-source, allows any developer to write their own Spout interface for any tool they choose.

We will use the [ofxSpout](https://github.com/prisonerjohn/ofxSpout) addon for openFrameworks. Note that if using the `master` branch, you will also need to download and install the [Spout SDK](http://spout.zeal.co/download-spout/).

#### Sender

The following app is a similar thresholding app with two senders.
* Note that `ofxSpout` only includes a `send()` function which takes an `ofTexture` reference as a parameter.

{% gist 515bd4b263b739c294ada300bd7ecdf2 ofApp-send.h %} 
{% gist 515bd4b263b739c294ada300bd7ecdf2 ofApp-send.cpp %}

#### Receiver

The following app holds two receivers corresponding to the two senders from above.
* We also need to create an `ofTexture` per receiver, and pass it as an argument to the `receive()` function.
* `ofxSpout::Receiver` holds a `selectSenderPanel()` function, which displays all available senders when called. Selecting a different sender in the panel overrides any settings made in the app.

{% gist 515bd4b263b739c294ada300bd7ecdf2 ofApp-recv.h %} 
{% gist 515bd4b263b739c294ada300bd7ecdf2 ofApp-recv.cpp %}

### NDI

Syphon and Spout are great for sharing images between applications, but they are limited to a single machine. We sometimes need to share images between different machines and that's where NDI comes in!

[NDI](https://www.ndi.tv/) is a high performance standard for video over IP. This means that it can be used to send images over the network with very low latency. The SDK is compatible with Windows, Mac, Linux, and even iOS and Android making it truly versatile.

Installing the NDI Tools is not necessary to use it depending on the toolkit you are using, but it does offer the advantage of advertising itself as a webcam. This means you could use an `ofVideoGrabber` as an NDI client directly in OF, you just need to select the device called "NewTek NDI Video".

While there are many options for using NDI under openFrameworks (see [here](https://github.com/thomasgeissl/ofxNDI), [here](https://github.com/leadedge/ofxNDI), and [here](https://github.com/nariakiiwatani/ofxNDI)), I have not been able to find a fully working addon. Some only include libs for Mac, others are using an older version of the NDI SDK. 

[ofxNDI](https://github.com/thomasgeissl/ofxNDI) by Thomas Geissl seems to be the best choice at the moment, but has some limitations:
* The receiver takes 10 seconds to start, during which it searches for senders on the network.
* The sender can only send a single texture at a time, because it does not have a way to differente feeds.
* Sending RGB images from Mac converts them to BGR under Windows. This can easily be fixed by swapping the red and blue channels in the image.

#### Sender

The following app only has a single `ofxNDISender`, and switches between them using the value of the `ofParameter` settings.

{% gist c1879fb72aaf87f6a4f0021e1e1c70a4 ofApp-send.h %} 
{% gist c1879fb72aaf87f6a4f0021e1e1c70a4 ofApp-send.cpp %}

#### Receiver

Because we only have a single sender, the receiver is simplified as a single `ofxNDIGrabber` that just gets drawn to the screen.

{% gist c1879fb72aaf87f6a4f0021e1e1c70a4 ofApp-recv.h %} 
{% gist c1879fb72aaf87f6a4f0021e1e1c70a4 ofApp-recv.cpp %}

If there is enough interest in the class to use NDI, I can try to update one of the addons to work across the board, just let me know!