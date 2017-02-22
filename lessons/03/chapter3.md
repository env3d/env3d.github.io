---
title: Creating A Complete Application
---
Before starting this lesson, you must understand

* Objects are created from Classes.
* Java programming is mostly about writing classes.
* Difference between local variables, parameters, and fields.
* Most Java programs contain multiple classes.
* Understanding the interactions between classes are extremely important in programming.
* In a typical program, there is usually 1 "controller" class that creates and manipulates objects of other classes.

# Using Multiple Classes

So far, we have been creating objects using the Env3D Extension in BlueJ.

These objects only interacts with the user.

In a real computer program, we have objects interacting with each other.

The Env3D library can be used directly to create standalone 3d applications.

First, download the template project, unzip it into a directory of your choice and open it with BlueJ.

Create a Game class as follows:

```java
import env3d.Env;

public class Game
{

    public Game()
    { 
    }


    public void play()
    {
        // A variable to determine if the game is finished
        boolean finished = false;
        
        // Create the env object so that we can manipulate
        // the 3D environment.
        Env env = new Env();        
        
        while (!finished)
        {
            // Gather the user input
            int key = env.getKey();

            // Terminate program when user press escape
            if (key == 1) {
                finished = true;
            } 
            
            // Update the environment
            env.advanceOneFrame();
        }
        

        // Exit the program cleanly
        env.exit();
    }
}
```

Create a new object of the Game class and run the play method, you'll get a blank environment where you are able to navigate using your mouse and the "W,S,D,A" key.

The Game class is what we call a controller class. It is responsible for the main game "loop" where the rest of the objects are being controlled. In particular, the game loop is responsible for the following:

* Gather user inputs
* Change the states of the various objects
* Update display

Every time the game loop runs, it checks the keyboard to see if a key has been pressed. The env.getKey() method returns a unique key code for each key on the keyboard. The unique key code for each key is found here.

The above program only shows a blank screen, since there are no objects in the environment for us to manipulate. To have a 3D object for us to look at and control, we need to add the object into the environment. First let's create a 3D object:

```java
public class Ball
{
    private double x, y, z;
    private double rotateY;
   
    public Ball(double x, double y, double z)

    {
        this.x = x;
        this.y = y;
        this.z = z;
    }
   
    /**
     * The method gets called every frame
     */
    public void move()
    {
        rotateY += 1;
    }
}
```

Using the above Ball class, we can create objects in our 3D space. The move() method, in particular, mutates the rotateY field every time it is called.

Now it is time to drop a ball into our environment, we modify the play() method from the Game class as follows:

```java
    public void play()
    {
        // A variable to determine if the game is finished
        boolean finished = false;
        
        // Create the env object so that we can manipulate
        // the 3D environment.
        Env env = new Env();        
        

        // Create a ball object

        Ball b = new Ball(5, 1, 5);

        // Add the object to the environment

        env.addObject(b);


        while (!finished)
        {
            // Gather the user input
            int key = env.getKey();

            // Terminate program when user press escape
            if (key == 1) {
                finished = true;
            } 
            
            // Update the environment
            env.advanceOneFrame();
        }
        

        // Exit the program cleanly
        env.exit();
    }
```

When you run the program now, you'll see a ball in the middle of the room. However, the ball it not very interesting, since the game loop does not manipulate it in any way.

To make things more interesting, let's change the ball's state inside the game loop by calling the ball's move() method, as follows:

```java
    public void play()
    {
        // A variable to determine if the game is finished
        boolean finished = false;
        
        // Create the env object so that we can manipulate
        // the 3D environment.
        Env env = new Env();        
        

        // Create a ball object

        Ball b = new Ball(5, 1, 5);

        // Add the object to the environment

        env.addObject(b);


        while (!finished)
        {
            // Gather the user input
            int key = env.getKey();

            // Terminate program when user press escape
            if (key == 1) {
                finished = true;
            } 


            // Move the ball

            b.move();
            
            // Update the environment
            env.advanceOneFrame();
        }
        

        // Exit the program cleanly
        env.exit();
    }
```

Every time the main game loop runs, the ball's move method is called. The move method causes the rotateY value of the ball to increase slightly, creating a spinning ball effect.

Believe it or not, we now have a complete program containing 2 classes. It has a controller to manage user input (it only response to the escape key), it changes the state of our objects (by calling the b.move() method), and updates the screen.

# Exercises

1. Add another ball into your environment, place them side by side, and make both of them spin.
2. Make the second ball spin in the opposite direction as the first ball.


Create the Doty class as shown below:

```java
/**
 * The Doty class models a 3D object
 * 
 */
public class Doty
{
    // x, y, and z is the location of doty
    private double x,y,z;
    
    // The texture of doty
    private String texture;
        
    /**
     * Constructor for objects of class Doty
     */
    public Doty()
    {
        x = 1;
        y = 1;
        z = 5;
        texture = "textures/doty.gif";
    }
}
```

<ol start="3">
    <li>Add a new constructor to the Doty class. This constructor will take 3 parameters: double x, double y, and double z. The purpose of this constructor is to make it possible to create a Doty object anywhere in the 3D space (instead of having it appear in the same spot every time).
    <li>In the Doty class, create a method void moveZ(double delta). Calling this method will change the z state variable by delta amount.
    <li>In the Doty class, create a method void moveX(double delta). Calling this method will change the x state variable by delta amount.
    <li>Add an object of the Doty class to your Game.
    <li>Program the main game loop so that when the UP/DOWN arrow is pressed, the moveZ() method in the Doty class will be called. You can find a list of key codes at here
    <li>Program the main game loop so that when the LEFT/RIGHT arrow is pressed, the moveX() method in the Doty class will be called.
    <li>Test your new program, you should now be able to control the Doty object using your arrow keys.
    <li>(Challenge) In the Doty class, create a method void move(). This method will be called every frame and will make Doty bounce up and down.

<object width="480" height="385">     <param name="movie" value="http://www.youtube.com/v/OD439OZsEzI?fs=1&amp;hl=en_US">     <param name="allowFullScreen" value="true">     <param name="allowscriptaccess" value="always"><embed type="application/x-shockwave-flash" src="http://www.youtube.com/v/OD439OZsEzI?fs=1&amp;hl=en_US" allowscriptaccess="always" allowfullscreen="true" width="480" height="385"></object> 
