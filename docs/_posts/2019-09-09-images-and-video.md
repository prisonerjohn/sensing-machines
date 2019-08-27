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

![](assets/images/shapes.svg){:height="360px" width="360px"}

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg
   ...
   height="512"
   width="512">
  ...
  <g
     transform="translate(0,-161.53332)"
     id="layer1">
    <circle
       style="stroke-width:0.26458332;fill:#00ffff;fill-opacity:1"
       r="52.916664"
       cy="229.26665"
       cx="67.73333"
       id="path3713" />
    <rect
       y="228.20831"
       x="5.2916665"
       height="63.5"
       width="63.5"
       id="rect4520"
       style="fill:#ff0000;fill-opacity:1;stroke-width:0.25843021" />
    <path
       id="path4524"
       d="M 49.514879,171.88985 123.5982,282.2589 Z"
       style="fill:none;stroke:#00b400;stroke-width:2.64583325;stroke-linecap:butt;stroke-linejoin:miter;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1" />
  </g>
</svg>
```

Pros:
  * Small file sizes, because minimal information is being stored.
  * Images can be scaled up without any quality loss or increase in file size. This is because the instruction set does not change, the only thing that changes is the point values.

Cons:
  * Low level of detail.
  * Limited types of effects, because we don't have all the image data available in the format.

## Raster Graphics

*Raster* formats define pixel values in a rectangular grid of pixels. The bigger the image, the greater the data set, and thus the larger the file size.

Some of the more common vector formats are `JPG`, `PNG`, `GIF`, and `TIF`.

![](assets/images/shapes.png){:height="360px" width="360px"}

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

# Image Formats

An image data structure usually comprises of a width and height, an array of pixels, and a pixel format.

Even though an image has 2 dimensions, a pixel array is usually one-dimensional, packing the rows one after the other.

![](/assets/images/grid-pix.png){:width="360px"}

## Pixel Index

Some frameworks allow accessing pixels using the column `x` and row `y`, like Processing. The following example reads the value of a pixel under the mouse cursor.

{% gist d139b58390dc3c2ad3de072329c86134 sm-01-getpixel-get.pde  %}

Note, however, that it is usually preferred to access the array directly as that tends to be much faster. In order to access the pixel in a 1D array using a 2D index, we first need to convert it.

This is done using the operation:

 ```java
 index = y * width + x
 ```

 The following example reads the value of a pixel under the mouse cursor, accessing the pixel array directly.

{% gist d139b58390dc3c2ad3de072329c86134 sm-01-getpixel-pixels.pde  %}

Conversely, if we want to get a 2D value from a 1D index, we need to use integer division:

```java
x = index % width
y = index / width
```

 The following example reads the value of a pixel sequentially, based on the sketch frame number.

{% gist d139b58390dc3c2ad3de072329c86134 sm-01-getpixel-seq.pde  %}

## Pixel Format

A standard color pixel will have 3 color channels: red, green, and blue (RGB). These will be packed sequentially in the array. Instead of each pixel holding a single value, it will hold 3.

![](/assets/images/grid-rgb.png){:width="360px"}

Processing packs all channels into a single `int`, but this is not the case for p5.js or openFrameworks. In most cases, the pixel array has total size:

```java
size = width * height * channels
```

Add example

RGB
Grayscale
Some have alpha but not for us
YUY

char 0-255
float 0-1
short 0-65535

Conway game of life

