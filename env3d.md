---
layout: page
title: About
permalink: /env3d/
---

So you want to learn programming, and have a little fun while doing it?  Maybe you already know Java programming, but wanted write your own 3D accelerated video games?  In either case, you've come to the right place.  This is the home of Env3D, a 3D engine written in the Java programming language.  What is so unique about Env3D is that it is designed with education in mind.  As a result, many of the more complicated tasks in traditional 3D engines are very easy to do in Env3D.  There are also lots of external resources built around Env3D to help people learn how to program, including a full set of lessons on object oriented programming, and tight intergration with the BlueJ IDE.

Unlike other specialized learning environment, Env3D uses a commerical quality 3D game engine underneath its easy-to-use user-interface.  This makes Env3D an extrememly powerful learning tool.  When students feel like they have outgrown Env3D, they can transition into other 3D engines naturally.

Another unique design goal of Env3D is easy deployment.  In most other learning environments, it is very hard for students to show others what they have done.  With Env3D, students can turn their projects into web ready applets with a click of a button, making it easy to share their games with friends and family, or even publish it to gaming websites.

So, let's get ready for a guided tour...

Before you start:

* Make sure that you have a recent version of Java installed on your system (OS X users already have it installed).  For Windows users, go to http://www.oracle.com/technetwork/java/javase/downloads/index.html and download the JDK.

* Download the BlueJ IDE from http://bluej.org.

* Download the Env3D engine.  It comes as a zip file.  Extract this file to a directory of your choice.  You should now have a directory named env3d_template.  You can delete the original zip file.

# The Development Environment

In the old days, progammers use text editors (like notepad on Windows), to write their computer programs.  After the program is written, the text file needs be converted (or compiled) to executable code (or machine code) so that it can be run on the machine.  Sometimes, programmers also utilize other tools to help with bug fixing and testing.  To be an effective programmer means being able to use multiple tools well.

While some programmers and some situations still require the use of multiple tools, nowadays, professional programmers often use an Intergrated Development Environment (IDE) to do their work.  An IDE puts together all of the tools that a programmer needs and package it into a common and consistent interface.  This helps programmers to be more productive as all the tools are "at their fingertips".

For students, using IDEs also simplfies the programming process, as students don't have to learn to use multiple seemingly unrelated tools.  The problem is that professional IDEs are often very complicated itself and requires a great deal of prior knowledge in order to use them effectively.  BlueJ is an IDE designed for students in mind, so BlueJ is the ideal IDE for us to use.

# Starting BlueJ

When you start BlueJ, you get a blank window.  Go to "Project->Open", then navigate to the place where you extracted your env3d_template directory.  You'll see the the env3d_template directory shows up as a BlueJ project.  Double click on it to open it.
The Env3D project

After you have opened the env3d_template project, you are once again faced with the main BlueJ window.  Only this time, all of the buttons are activited, the center window is still empty.  The env3d_template project does not contain any computer program.  It is an empty project as far as the programmer is concerned.  However, behind the scene, the env3d_template project contains the actual 3D engine libraries.  You must write your own game code inside the env3d_template project, as this is the only way your code can interact with the Env3D engine.  The env3d_template project also contains a BlueJ plugin to add features like object visualization and applet creation to the IDE.

To check that everything is setup properly, go to the "Tools" menu.  You should see a "Start Env3D" item.  Click on it and a 3D window will popup, with an empty room.  Press escape to close the 3D window.

You are now ready to follow the lessons.

Have fun programming your 3D games!
