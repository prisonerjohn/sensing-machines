---
layout: post
title: "Assignment 0"
categories: assignments
---

This is an optional, easier assignment that can be delivered instead of [Assignment 2]({{ site.baseurl }}{% post_url 2019-09-30-assignment-2 %}).

This is an alternative for students who are having difficulties with C++ and openFrameworks, and want to make sure they have the basics right before diving in too deep. 

### Conway's Game of Life

[Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) is a cellular automata simulation where a 2D grid of cells evolve between alive and dead states.
* The Game of Life is a zero-player game, where you just set the initial state and the rules, and watch the evolution happen.
* A cell's state in the next frame depends on the state of its immediate neighbors in the current frame. It is set by counting the number of live or dead neighbors and applying the corresponding rule.
* Depending on this initial state and rules, interesting patterns can emerge where cells can appear to oscillate or travel across the board.

### Instructions

Your assignment is to build your version of the Game of Life using openFrameworks. This will give you an opportunity to play with an image's pixel data. 

* Use an `ofImage` as your 2D canvas. 
* Set `0` as the pixel value for a dead cell, and `255` as the pixel value for a live cell.
* Neighbors are the pixels on the top-left, top-center, top-right, middle-left, middle-right, bottom-left, bottom-center, and bottom-right.
* Make sure to handle edge cases appropriately! You can either ignore the invalid neighbors or wrap around the texture.

While Conway's version has its own specific rules, our version will use rules based on your NYU ID.

1. The first number in your ID is the number of dead neighbors required for a live cell to die.
1. The last number in your ID is the number of live neighbors required for a dead cell to come alive.
1. If the first and last number of your ID are the same, use the middle number instead for rule 2.

For example, my NYU ID is ez377. Here is some pseudo-code representing my rules:
```
for (each cell in image):
    if (cell is dead):
        count live neighbors
        if (num live neighbors == 7):
            cell comes alive!
    else:
        count dead neighbors
        if (num dead neighbors == 3):
            cell dies :(
```

<video src="{{ site.baseurl }}/assets/videos/game-of-life.mp4" controls width="360px"></video>

### Delivery

* Name your project `SM00_FirstLast` where *First* is your first name and *Last* is your last name.

```
- OF/
  - apps/
    - SensingMachines/
      - SM00_ElieZananiri/
        - src/
        - bin/
        - addons.make
        - SM00_ElieZananiri.sln
        - SM00_ElieZananiri.vcxproj
        - ...
```

* Only submit the necessary files to rebuild your project. 
  * This includes sources, the `addons.make` file, and any resources in your `data` folder. 
  * No project or compiled files. 
  * In the example above, you would only keep the `src` folder, `addons.make` file, and `bin/data` if you are using any external assets. 
  * Zip the `SM00_ElieZananiri` parent directory.

```
    - SensingMachines/
      - SM00_ElieZananiri/
        - src/
        - addons.make
```

* **OPTIONAL** In true ITP fashion, you can make a blog post about your project. If you do, please send me the link!

* Email your project to [ez377@nyu.edu](mailto:ez377@nyu.edu).
  * Send the package ZIP as an attachment.
  * If that does not work, upload it to Google Drive and send me the link.
  * If you made a blog post or added your project to GitHub, send me a link to that too.

* Come to class with a working project on a working computer, and be prepared to talk and answer questions about it. Time allowing, some of you will demo your projects to the class!

Thank you!