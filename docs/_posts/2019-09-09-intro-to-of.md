---
layout: post
title: "Intro to openFrameworks"
categories: notes
---

## What is openFrameworks?

[openFrameworks](https://openframeworks.cc/) (OF) is an open source cross-platform C++ toolkit designed to assist the creative process, by providing a simple and intuitive framework for experimentation.

OF is distributed under the MIT License, which gives everyone the freedoms to use openFrameworks in any context:
* Commercial or non-commercial.
* Public or private.
* Open or closed source.

### What is C++?

* A programming language.
* General purpose.
* Fairly low level, but can be programmed in a high level way.
* Compiled (it's really fast).
* Widely used.

### Libraries

OF is written in C++. It makes it easier to interface with the many libraries that have been written in C and C++ without needing to rely on a wrapper for another language.

Libraries are collections of code that do something common or useful. For example:

* [OpenGL](https://opengl.org/) for drawing graphics.
* [FreeType](https://www.freetype.org/) for loading and rendering fonts.
* [FreeImage](http://freeimage.sourceforge.net/) for loading image files.
* [AVFoundation](https://developer.apple.com/av-foundation/) for playing videos.

OF is the glue that ensures these libraries work together well.

![]({{ site.baseurl }}/assets/images/of-glue.png)

It is a consistent and intuitive interface to these libraries.

For example, loading a font using FreeType directly would look something like this:

```cpp
FT_New_Face(...);
FT_Set_Char_Size(...);
```

And with OF would look like this:

```cpp
ofTrueTypeFont font;
font.load(...);
```

Loading an image using FreeImage directly:

```cpp
FreeImage_OpenMemory(...);
FreeImage_LoadFromMemory(...);
FreeImage_GetBits(...);
```

And with OF:

```cpp
ofImage img;
img.load(...);
```

### Open Source

OF is distributed as source code.
* An open book, giving the curious a good starting point for learning about C++ library wrangling.
* A work in progress, keeping the code visible allowing for easier changes and feedback.
* An invitation for users to modify the toolkit to their taste or needs.

Over 70 people have contributed to the core, and there are more than 1500 addons extending the base functionality of the toolkit.

### Comparisons with Processing

openFrameworks and Processing have many similarities. In fact, OF is inspired by Processing! 

When possible, openFrameworks tries to maintain parity with Processing, making moving from one to the other very easy. Compare the following code snippets:

```java
void setup()
{
  frameRate(60);
  background(0);
}

void draw()
{
  fill(255, 0, 0);
  rect(10, 10, 50, 50);
}
```

```cpp
void ofApp::setup()
{
  ofSetFrameRate(60);
  ofBackground(0);
}

void ofApp::draw()
{
  ofSetColor(255, 0, 0);
  ofDrawRectangle(10, 10, 50, 50);
}
```

<details>
	<summary>What does the <code>::</code> mean?</summary>
	<p markdown="1">
	`::` is a scope resolution operator in C++. It is used to show the relationship between methods (functions) and classes. Methods can be defined anywhere in the source code, so we need a way to know where they belong when they are defined.
	</p>
	<p markdown="1">
	For example, `void ofApp::draw()` means "define the `draw()` function that belongs to the `ofApp` class".
	</p>
</details>

## Getting Started

### Installation

[Download](https://openframeworks.cc/download/) the openFrameworks package for your environment.

Follow the corresponding setup guide.
* Unlike Processing, OF does not come with its own development environment (IDE). Instructions to set this up will be included in the guide.

### Project Generation

To create a new project, you are strongly encouraged to use the OF Project Generator. 
* This application can be found in your downloaded package, under `/path/to/OF/projectGenerator-XXX`. 
* The Project Generator will take care of adding any files and libraries needed to build your applications.

The first time you run the Project Generator, you'll be asked to set the path to the openFrameworks installation on your system.

![]({{ site.baseurl }}/assets/images/of-pg-path.png){:width="360px"}

You can then create a project by giving it a name and a save path. It is recommended to save your projects under `path/to/OF/apps/SensingMachines/`.

![]({{ site.baseurl }}/assets/images/of-pg-proj.png){:width="360px"}

Click `Generate` to create the project files. Once that is complete, you can click on the `Open in IDE` button to open the project.

![]({{ site.baseurl }}/assets/images/of-pg-done.png){:width="360px"}

### Anatomy of an OF Project

A basic OF project will include three files you can edit.

`main.cpp` contains the `main()` function. This is the entry point to the program.

The main function is where the application window is set. You can set up the window dimensions, renderer used, graphics quality, additional windows, etc.

{% gist 04e3715543fff7e0d2e15bf19ebe4f40 main.cpp  %}

The other two files define the `ofApp` class. You can think of `ofApp` as the main class that holds and runs all the components belonging to your program, kind of like a sketch in Processing.

In C++, classes are defined in two parts: the header (declaration) and the implementation (definition). The header defines *what* a class is, and the implementation defines *how* a class operates.

The header will usually have extension `.h` or `.hpp`. This is where all variables and methods in the class are listed. You can think of this as a table of contents for the class. When classes link to each other, they will only refer to the header class as they only need to "know" what variables and methods are available to them, and not how these are implemented. This reduces dependencies and in turn compilation times.

{% gist 04e3715543fff7e0d2e15bf19ebe4f40 ofApp.h  %}

The implementation will have extension `.cpp`. This is where all the methods declared in the header are defined.

{% gist 04e3715543fff7e0d2e15bf19ebe4f40 ofApp.cpp  %}

Note that the placeholder `ofApp` already has stubs for common methods you may want to use. You can keep these in or delete them, but if you get rid of them you'll need to do so in both the header and the implementation files.

### Reference

OF ships with a multitude of examples in the `path/to/OF/examples` folder, and this is the best way to get familiar with the tool. Note that project files need to be created for these using the Project Generator before they can be built.

OF also has comprehensive [documentation](https://openframeworks.cc/documentation/) on its website, as well as an active [user forum](https://forum.openframeworks.cc/), which are other great places to get information.
