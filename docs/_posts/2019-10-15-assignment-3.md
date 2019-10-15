---
layout: post
title: "Assignment 3"
categories: assignments
---

### Instructions

Create two applications and use a communication protocol to exchange information between them.

* At least one of your applications needs to be written in openFrameworks. The other can be OF or anything else.

* You can build off of a previous assignment or class exercise. For example, you can send blobs from the [color tracker](({{ site.baseurl }}{% post_url 2019-09-23-object-tracking %})) to a different application to control another object.

* Make sure to use an appropriate method of communication. Think of reliability, speed, data size, etc. when making your decision.

* If you have an idea but are not sure how to do it, ask about it in the Telegram channel and we can break it down and figure it out together. 

* Expose your parameters and use a GUI or some form of controller to tweak them for best results.

* Some of you will demo your project in class. Your effect should therefore work in the conditions of our classroom (size, layout, light, etc.)

You have two weeks to prepare your assignment.

Due on **Oct 28 2019** at **6:30pm** (start of class).

### Delivery

* Name your project `SM03_FirstLast` where *First* is your first name and *Last* is your last name.

```
- OF/
  - apps/
    - SensingMachines/
      - SM03_ElieZananiri/
        - src/
        - bin/
        - addons.make
        - SM03_ElieZananiri.sln
        - SM03_ElieZananiri.vcxproj
        - ...
```

* Only submit the necessary files to rebuild your project. 
  * This includes sources, the `addons.make` file, and any resources in your `data` folder. 
  * No project or compiled files. 
  * In the example above, you would only keep the `src` folder, `addons.make` file, and `bin/data` if you are using any external assets. 
  * Zip the `SM03_ElieZananiri` parent directory.

```
    - SensingMachines/
      - SM03_ElieZananiri/
        - src/
        - bin/data/
        - addons.make
```

* **OPTIONAL** In true ITP fashion, you can make a blog post about your project. If you do, please send me the link!

* Email your project to [ez377@nyu.edu](mailto:ez377@nyu.edu).
  * Send the package ZIP as an attachment.
  * If that does not work, upload it to Google Drive and send me the link.
  * If you made a blog post or added your project to GitHub, send me a link to that too.

* Come to class with a working project on a working computer, and be prepared to talk and answer questions about it. Time allowing, some of you will demo your projects to the class!

Thank you!