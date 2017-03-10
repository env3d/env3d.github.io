---
title: Doty feeding exercise
---
In this exercise, you will be creating a simple game to move a Doty object around the room in search for food.

# Step 1

Download the template project and add the Game class and the Doty class from the bottom of this document. Currently, Doty is always facing the camera. Modify this project so that when the user moves Doty in a particular direction, Doty will also turn to face that direction (i.e. if the user press the left arrow key, Doty will turn to face left as well). You will need to add a rotateY state variable to the Doty class. Make it so that Doty cannot move outside the room (10x10x10).

# Step 2

Add a new class call "Food". Use a parameterized constructor for the Food class so that the caller can control where to place each food. Put 3 food objects into the game.

# Step 3

Modify your project such that when the user moves Doty close to a food, the food would disappear and Doty would grow in size by a certain amount (simulating "eating" of a food). Every time Doty moves, it will decrease in size by some other amount. The game ends when Doty finished all three foods or when Doty dies of starvation.

Hint: to check if your Doty object is close to a Food object, use the distance() method provided in the Game class.

Your final program should look similar to the following video:

<iframe width="560" height="315" src="https://www.youtube.com/embed/yglFOC5MPeI" frameborder="0" allowfullscreen></iframe>

# Code Listing

Game class

```java
import env3d.Env;

public class Game
{
    private Doty doty1;
    
    public Game()
    {
        doty1 = new Doty(5,1,9);        
    }
    
    public void play()
    {
        boolean finished;
        finished = false;
        
        Env env;
        env = new Env();
        
        // Adding doty to the environment
        env.addObject(doty1);

        // Disable the default keyboard and mouse control
        env.setDefaultControl(false); 

        // Fix the camera location
        env.setCameraXYZ(5,9,13);
        env.setCameraPitch(-45);

        
        // The game loop
        while (finished == false)
        {
            int currentKey = env.getKey();
            
            if (currentKey == 1) {
                // escape button
                finished = true;
            } else if (currentKey == 200) {
                // Up arrow
                doty1.moveZ(-0.3);
            } else if (currentKey == 208) {
                // Down arrow
                doty1.moveZ(0.3);
            } else if (currentKey == 203) {
                // Left arrow
                doty1.moveX(-0.3);                
            } else if (currentKey == 205) {
                // Right arrow
                doty1.moveX(0.3);
            }
          
            env.advanceOneFrame();
        }
        
        env.exit();
    }
    
    private double distance(double x1, double x2, double y1, double y2, double z1, double z2) {
        double xdiff, ydiff, zdiff;
        xdiff = x2 - x1;
        ydiff = y2 - y1;
        zdiff = z2 - z1;
        return Math.sqrt(xdiff*xdiff + ydiff*ydiff + zdiff*zdiff);
    }
}
```

Doty class

```java
public class Doty
{

    private double x;
    private double y;
    private double z;
    private String texture;
    
    public Doty(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        texture = "textures/doty.gif";
    }
    
    public void moveX(double delta)
    {
        x = x + delta;
    }
    
    public void moveZ(double delta)
    {
        z = z + delta;
    }
}
```
 
