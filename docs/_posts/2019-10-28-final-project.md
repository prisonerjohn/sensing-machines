---
layout: post
title: "Final Project"
categories: assignments
---

Create a fully fleshed out interactive. This can take many shapes:

* Reactive installation
* Performance
* Game
* Product prototype
* Something else

### Instructions

* Team up in groups of 2.
  * You can be 1 or 3 in a team if it makes sense. Project complexity should be proportional to the size of your group.
  * Clearly define everyone's roles.
  * If you have an idea and are looking for a partner, propose it in the Telegram channel!

* Use a sensor!
  * Make sure you can get access to the sensor you want to use, either as a purchase or loan from the ER.
  * Pick something that will work best for your setup. For example, if you need to track cards on a table, don't use your laptop's built-in webcam.

* Use a computer as a sensing device.
  * A computer means a PC, phone, Raspberry Pi, or anything OF runs on.
  * You can use many networked devices if needed.

* Use openFrameworks to write your sensing code.
  * You can use OF for the entire project, or you can hand-off to other tools like Unity, Processing, MadMapper, etc.
  * If you have a hand-off, use a communication protocol that makes sense for your data.
  * All parts of your project have to work, even the non-OF ones.

* Use some of the techniques we covered in class AND something new!
  * We will have time to go over new topics in the remaining classes, so as long as you stay realistic, don't limit yourself to what you already know.

* Break the project up into digestible modules.
  * Think of how the logic can be split up.
  * Think of what needs a proof of concept, what needs a prototype, etc.

* Expose your parameters and use a GUI or some form of controller to tweak them for best results.
  * Take advantage of the GUI save / load function.
  * Include both "setup" and "presentation" modes, where the GUI is shown / hidden, the canvas takes up a window / full screen, etc.

For all points above, pick the right tool for the job.

### Schedule

You have 4 weeks to prepare your project. You will need to present something to the class every week: a proposal, two milestones with updates and work in progress, a final working project.

#### Nov 4 / Project Proposal

* Present the idea.
  * What does it do?
  * Why does it do that?
  * How is it presented? What is your plan for presenting it in class?
  * What are some references that inspired you?

* Analyze the components.
  * What are the different components of the project?
  * How will the work be divided?
  * What are you comfortable with? What is completely unknown? What is risky?

* Make a plan.
  * What research will you do?
  * What components will you prototype?
  * What will you present for the two milestones?
  * When and how will you combine work together?

#### Nov 11 / Milestone 1

* Progress update.
  * Did your scope change? If so, why?
  * What did you do last week?
  * Are you still on track?
  * Do you need help with anything?

* Prototype presentation.
  * Show working or failed prototypes.
  * Talk through problems faced and how you resolved them.

#### Nov 18 / Milestone 2

* Progress update.
  * Did your scope change? If so, why?
  * What did you do last week?
  * Are you still on track?
  * Do you need help with anything?

* Prototype presentation.
  * Show working or failed prototypes.
  * Talk through problems faced and how you resolved them.

#### Nov 25 / Final Presentation

* Present the project.
  * It needs to be set up and working!
  * If you have special requirements, take enough time to gather what you need.

* Postmortem
  * Did your scope change from the initial design? Why and how?
  * What are you satisfied with? What needs improvement?
  * If you had to start over, would you change anything?

### Delivery

#### Progress

You will need to submit your progress online.

* Use a blog, GitHub page, or something similar.
* Post everything you will present in class every week.
* Send me the link!

#### Code

* Submit a package including everything I need to build and run your project.
  * Create a top-level project named `SM99_LastLast` where *Last* is every teammate's last name. For example, if I work with Jane Doe, my project will be called `SM99_ZananiriDoe`.
  * If you use many frameworks, use directories to differentiate them.
  * If you use many apps, use directories to differentiate them.

```
- SM99_ZananiriDoe/
  - OF/
    - ServerVideo/
      - ...
    - ServerAudio/
      - ...
  - Unity/
    - ClientWorld/
      - ...
```

* Only submit the necessary files to rebuild your project. Using OF as an example:
  * This includes sources, the `addons.make` file, and any resources in your `data` folder. 
  * No project or compiled files. 
  * In the example above, you would only keep the `src` folder, `addons.make` file, and `bin/data` if you are using any external assets. 

```
- SM99_ZananiriDoe/
  - OF/
    - ServerVideo/
      - src/
      - bin/data/
      - addons.make
  - ...
```

* Package everything up as a ZIP.
  * Make the ZIP available on your blog, either directly or upload it to a service like Google Drive and post the link.

Thank you!