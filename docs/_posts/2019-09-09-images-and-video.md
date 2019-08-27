---
layout: post
title: "Images and Video"
categories: notes
---

# File Formats

Digital images come in a variety of formats, each with their own properties.

## Vector Graphics

*Vector* formats define a set of points and instructions on how to draw them. The instructions are run by a program to raster the image in order to view it. 

Some of the more common vector formats are `SVG`, `EPS`, `PDF`, and `AI`.

If we open the following `SVG` file in a text editor, we will notice that it is fairly easy to read the format. It almost reads like a Processing program :)

![]({{ site.baseurl }}/assets/images/shapes.svg){:height="360px" width="360px"}

{% gist 584d166a85fdf268273d78e6979d29f0 %}

Pros:
  * Small file sizes, because minimal information is being stored.
  * Images can be scaled up without any quality loss or increase in file size. This is because the instruction set does not change, the only thing that changes is the point values.

Cons:
  * Low level of detail.
  * Limited types of effects, because we don't have all the image data available in the format.

## Raster Graphics

*Raster* formats define pixel values in a rectangular grid of pixels. The bigger the image, the greater the data set, and thus the larger the file size.

Some of the more common vector formats are `JPG`, `PNG`, `GIF`, and `TIF`.

![]({{ site.baseurl }}/assets/images/shapes.png){:height="360px" width="360px"}

Pros:
  * High quality and detail, especially at high resolutions.
  * More advanced image effects, because every pixel can be edited.

Cons:
  * File sizes tend to be bigger.
  * Images lose quality when scaled up.

In order not to end up with huge file sizes, many raster formats are compressed. Some compression methods are lossy, meaning that some of the data is lost when it is compressed, and others are lossless, meaning that all the data is recovered once the data is uncompressed.

## Video

Videos are just a series of images that need to be processed and displayed very quickly. 

Video formats are always rasters and are mostly compressed. Some formats are simply extensions of their image counterparts, like `Motion JPG` for example, which is just a series of `JPG`-compressed frames. Others are specific to video, like `H.264`, which has a form of compression over time, where some pixels are predicted based on known pixels in previous and future key frames. 

Efficient compression is necessary for video because of the huge amount of data that it carries. While film used to run at 24 frames per second, high definition video now runs standard at 60 frames per second, and sometimes goes as high as 240 fps! Combining these fast frame rates with large resolutions like 4K means that hundreds of millions of pixels need to be processed every second for a video to play smoothly.

## Processing Images

When working with image data, we will usually want to work with rasterized uncompressed images. This is because many algorithms require looping efficiently through all pixels in an image, or doing quick look-ups between neighboring pixels. 

The good news is that this usually happens in the image loader or video codec, before an image or video frame gets to us. While we will almost never have to worry about decoding an image or a video frame ourselves, we should still be mindful of what format the data comes in, and make sure that it is suitable for our application.

# Image Attributes

An image data structure usually comprises of a width and height, an array of pixels, and a pixel format.

Even though an image has 2 dimensions, a pixel array is usually one-dimensional, packing the rows one after the other.

![]({{ site.baseurl }}/assets/images/grid-pix.png){:width="360px"}

Some frameworks allow accessing pixels using the column `x` and row `y`, like [`PImage.get()`](https://www.processing.org/reference/PImage_get_.html) in Processing and [`ofImage.getColor()`](https://openframeworks.cc//documentation/graphics/ofImage/#!show_getColor) in openFrameworks. 

The following example reads the value of a pixel under the mouse cursor.

{% gist 283262100c7d748a437f255a9a2a1415 sm-01b-ofApp-getCoord.cpp  %}

## Pixel Access

A standard color pixel will have 3 color channels: red, green, and blue (`RGB`). While Processing packs all channels into a single `int`, this is not common practice. The color values are usually packed sequentially in the array; instead of each pixel holding a single value, it will hold 3.

![]({{ site.baseurl }}/assets/images/grid-rgb.png){:width="360px"}

The pixel array then has total size:

```cpp
size = width * height * channels
```

In order to access the pixel in a 1D array using a 2D index, we first need to convert it.

 ```cpp
 index = y * width + x
 ```

How do we access a pixel index in an `RGB` image?

Because each pixel has three color values (for each `RGB` channel), we need to multiply our pixel index by `3` to take that offset into account.

```cpp
pixel = y * width + x
index = pixel * 3
index = (y * width + x) * 3
```

The following example reads the value of a pixel under the mouse cursor, accessing the pixel array by index.

{% gist 283262100c7d748a437f255a9a2a1415 sm-01c-ofApp-getIndex.cpp  %}

Note the use of [`ofPixels.getNumChannels()`](https://openframeworks.cc//documentation/graphics/ofPixels/#!show_getNumChannels) instead of the literal `3`. This ensures the code will work with all image types and not just RGB images.

Conversely, if we want to get a 2D value from a 1D index, we can use integer division:

```cpp
x = index % width
y = index / width
```

 The following example reads the value of a pixel sequentially, based on the sketch frame number.

{% gist 283262100c7d748a437f255a9a2a1415 sm-01d-ofApp-getFrame.cpp  %}

## Image Format

The most common image type we will work with is `RGB` color images. 

We will also work with single-channel formats, usually called grayscale or luminance. These are particularly handy for devices that only capture a brightness level, like infrared cameras or depth sensors.

Some images also have an alpha channel for transparency, like `RGBA`. Our example image happens to have transparency, but we will encounter this rarely in this class as most sensors do not use the alpha channel.

Another format worth mentioning is [`YUV`](https://en.wikipedia.org/wiki/YUV), which is a color encoding that is based on the range of human perception. Instead of using three channels for color, it uses one for brightness and two for color shift. This gives similar results to `RGB` but at much smaller sizes (usually a third), and this is why `YUV` formats are often used for webcam streams.

## Pixel Format

Pixel color values can be stored in a few different formats. The more bits a format can hold, the more range the values can have, and the larger the size of the frame gets.

* `unsigned char` is the most common format. It uses integers and each channel has 8 bits of data and values range from `0` to `255`.
* `float` uses floating point 32-bit data. The usual range is from `0.0` to `1.0` but this format can be used for [HDR](https://en.wikipedia.org/wiki/High-dynamic-range_imaging) effects, where the values can extend past `1.0` or for storing non-color data, where we can even use negative values. We will use `float` a lot when working with depth sensors.
* `unsigned short` is another integer format but with 16-bits of data, meaning values range from `0` to `65535`. We will use this format a lot too when working with depth sensors, where precision is very important.

The following example reads the value of a pixel under the mouse cursor, accessing the pixel array directly.

{% gist 283262100c7d748a437f255a9a2a1415 sm-01e-ofApp-getData.cpp  %}

While accessing data arrays directly is a bit more complicated, it tends to be much faster and is the recommended approach when having to process large images pixel by pixel.

