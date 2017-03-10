---
title: Basic Inheritance
---
Before starting this lesson, you should know the following concepts:

* Basic use of the extends keyword.

# Creating a simulation

Very often, computers are used to create simulations. Think of a simulation as a game that the player only specifies the initial conditions. There are no user interactions after the program starts.

What we will be building is a simple simulation of "predator and prey". Let's use the Fox class as the predator and the Tux class as prey, as follows:

```java
public class Fox 
{

    private double x, y, z, scale;
    private String texture, model;
    
    public Fox(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        texture = "models/fox/fox.png";
        model = "models/fox/fox.obj";
        scale = 1;
    }
    
    public void move() 
    {
        int rand = (int) (Math.random()*4);
        
        if (rand == 0) {
            x += scale;
        } else if (rand == 1) {
            x -= scale;
        } else if (rand == 2) {
            z += scale;
        } else if (rand == 3) {
            z -= scale;
        }

        if (x < scale) x = scale;
        if (x > 50-scale) x = 50 - scale;
        if (z < scale) z = scale;
        if (z > 50-scale) z = 50 - scale;
    }
    
    /**
     * Accessor to get the x value 
     */
    public double getX()
    {
        return x;
    }

    /**
     * Accessor to get the y value 
     */
    public double getY()
    {
        return y;
    }

    /**
     * Accessor to get the z value 
     */
    public double getZ()
    {
        return z;
    }

    /**
     * Accessor to get the scale value 
     */
    public double getScale()
    {
        return scale;
    }
} 
```

```java
public class Tux 
{

    private double x, y, z, scale;
    private String texture, model;
        
    public Tux(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        texture = "models/tux/tux.png";
        model = "models/tux/tux.obj";
        scale = 1;
    }
    
    public void move() 
    {                
        int rand = (int) (Math.random()*4);
        
        if (rand == 0) {
            x += scale;
        } else if (rand == 1) {
            x -= scale;
        } else if (rand == 2) {
            z += scale;
        } else if (rand == 3) {
            z -= scale;
        }
        if (x < scale) x = scale;
        if (x > 50-scale) x = 50 - scale;
        if (z < scale) z = scale;
        if (z > 50-scale) z = 50 - scale;
    }
    
    /**
     * Accessor to get the x value 
     */
    public double getX()
    {
        return x;
    }

    /**
     * Accessor to get the y value 
     */
    public double getY()
    {
        return y;
    }

    /**
     * Accessor to get the z value 
     */
    public double getZ()
    {
        return z;
    }

    /**
     * Accessor to get the scale value 
     */
    public double getScale()
    {
        return scale;
    }
}
```

We also need the Game class to control the simulation. The main thing to pay attention to is that the simulation logic is inside the game loop. It checks the distance between each fox and tux using a nested for loop. When a fox is close to a tux, the tux disappears.

Another thing to note is that we cannot remove the tux object inside the nested loop itself (this is a limitation of list processing using for loop), we have to store all the dead tuxes into another list and remove the dead tuxes after the nested loop is done.

```java
import env3d.Env;
import java.util.ArrayList;

/**
 * A predator and prey simulation.  Fox is the predator and Tux is the prey.
 */
public class Game
{
    private ArrayList<Fox> foxes;
    private ArrayList<Tux> tuxes;
    private Env env;    
    private boolean finished;
    
    /**
     * Constructor for the Game class. It sets up the foxes and tuxes.
     */
    public Game()
    {
        // we use a separate ArrayList to keep track of each animal. 
        // our room is 50 x 50.
        foxes = new ArrayList<Fox>();
        for (int i = 0; i < 5; i++) {
            foxes.add(new Fox((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));
        }
        
        tuxes = new ArrayList<Tux>();
        for (int i = 0; i < 50; i++) {
            tuxes.add(new Tux((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));
        }
    }
    
    /**
     * Play the game
     */
    public void play()
    {
        
        finished = false;
        
        // Create the new environment.  Must be done in the same
        // method as the game loop
        env = new Env();
        
        // Make the room 50 x 50.
        env.setRoom(new Room());
        
        // Add all the animals into to the environment for display
        for (Fox f : foxes) {
            env.addObject(f);
        }
        
        for (Tux t : tuxes) {
            env.addObject(t);
        }
        
        // Sets up the camera
        env.setCameraXYZ(25, 50, 55);
        env.setCameraPitch(-63);
        
        // Turn off the default controls
        env.setDefaultControl(false);
        
        // A list to keep track of dead tuxes.
        ArrayList<Tux> dead_tuxes = new ArrayList<Tux>();
        
        // The main game loop
        while (!finished) {            

            if (env.getKey() == 1)  {
                finished = true;
             }
        
            // Move each fox and tux.
            for (Fox f : foxes) {
                f.move();
            }
            
            for (Tux t : tuxes) {
                t.move();
            }
                        
            // Simulation logic, if a fox touches a tux, the tux will be dead.
            for (Fox f : foxes) {
                for (Tux t : tuxes) {
                    if (distance(f.getX(), t.getX(), f.getY(), t.getY(), f.getZ(), t.getZ()) < f.getScale()+t.getScale()) {
                        // Put the tux into the dead list - to be cleaned up later.  If we try to remove
                        // the tux here, we will get an exception as Java does not allow modification of the list
                        // while we are processing it.
                        dead_tuxes.add(t);                        
                    }
                }
            }
            
            // Clean up of the dead tuxes.
            for (Tux t : dead_tuxes) {
                env.removeObject(t);
                tuxes.remove(t);
            }
            // we clear the ArrayList for the next loop.  We could create a new one 
            // every loop but that would be very inefficient.
            dead_tuxes.clear();
        
            // Update display
            env.advanceOneFrame();
        }
        
        // Just a little clean up
        env.exit();
    }
    
    
    /**
     * A function to calculate the distance between 2 objects in 3 dimension.
     */
    private double distance(double x1, double x2, double y1, double y2, double z1, double z2) {
        double xdiff, ydiff, zdiff;
        xdiff = x2 - x1;
        ydiff = y2 - y1;
        zdiff = z2 - z1;
        return Math.sqrt(xdiff*xdiff + ydiff*ydiff + zdiff*zdiff);
    }
    
    /**
     * Main method to launch the program.
     */
    public static void main(String args[]) {
        (new Game()).play();
    }
}
```

Finally, a 50x50 room:

```java
public class Room
{
    private double width, depth, height;
    private String textureTop,  textureBottom, textureEast, textureWest, textureSouth, textureNorth;
    
    /**
     * A room with just marble floor and no walls.
     */
    public Room()
    {
        width = 50;
        depth = 50;
        height = 50;
        textureBottom = "textures/marble.png";
    }
}
```

# Code duplication

If you look at the above program, you will noticed that the Tux and Fox classes are very similar. In fact, other than the constructor, the rest of the code is exactly the same! This kind of code duplication is not optimal. If we want to change some common aspects of both classes, we'll have to make changes in 2 places. This increases the chance of errors and reduces maintainability. So far, we have not learned of a way to reduce code duplication between classes.
Using inheritance

The basic idea of inheritance is to solve the above problem. If we have 2 classes that shares very similar structure, could we write the common components only once? First, let's extract the common sections between the Fox and the Tux classes into a new class, called Creature:

```java
/**
 * A generic creature
 */
public class Creature
{

    private double x, y, z, scale;
    private String texture, model;
    
    /**
     * Constructor, need to provide a location
     */
    public Creature(double x, double y, double z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 1;        
    }
    
    /**
     * Move the creature randomly
     */
    public void move() 
    {                
        double rand = Math.random();
        if (rand < 0.25) {
            x += scale;
        } else if (rand < 0.5) {
            x -= scale;
        } else if (rand < 0.75) {
            z += scale;
        } else if (rand < 1) {
            z -= scale;
        }        

        if (x < scale) x = scale;
        if (x > 50-scale) x = 50 - scale;
        if (z < scale) z = scale;
        if (z > 50-scale) z = 50 - scale;

    }
    
    /**
     * Accessor to get the x value 
     */
    public double getX()
    {
        return x;
    }

    /**
     * Accessor to get the y value 
     */
    public double getY()
    {
        return y;
    }

    /**
     * Accessor to get the z value 
     */
    public double getZ()
    {
        return z;
    }

    /**
     * Accessor to get the scale value 
     */
    public double getScale()
    {
        return scale;
    }    
    
    /**
     * Mutator to set the texture
     */
    public void setTexture(String texture) 
    {
        this.texture = texture;
    }

    /**
     * Mutator to set the model
     */
    public void setModel(String model) 
    {
        this.model = model;
    }
}
```

Notice that the creature only has the common parts of Tux and Fox. The code that is unique to each class (setting the texture and model) are not here.

Once we have the common parts extracted out, we can now write out Tux and Fox classes as follows:

```java
public class Tux extends Creature
{   
    public Tux(double x, double y, double z)
    {        
        super(x, y, z);
        
        // Must use the mutator as the fields have private access
        // in the parent class        
        setTexture("models/tux/tux.png");
        setModel("models/tux/tux.obj");
    }  
}
```

```java
public class Fox extends Creature
{
    public Fox(double x, double y, double z)
    {
        super(x, y, z);
        
        // Must use the mutator as the fields have private access
        // in the parent class
        setTexture("models/fox/fox.png");
        setModel("models/fox/fox.obj");
    }    
}
```

Wow! What a difference from before. Both of these classes are now just the constructor (the part that is unique). So what happens when I create a Fox or a Tux object? The keyword extends tells java that an object of the Fox class will contain all of the fields and methods of the Creature class.

You'll notice that the first thing that happens in the constructor of both Fox and Tux is a call to super(x, y, z). This is a call to the parent's constructor, so that x, y and z can be set properly by Creature's constructor.

In my program, when I call the move() method of a Fox object, java will first try to look for the move() method in the Fox class. Since the move() method is not inside the Fox class, java will then search the Creature class for the move() method and execute that.

Our program now runs exactly as before, but with much cleaner code. No additional changes are needed in the Game class.

# Exercises

* Make it so that when a fox eats a tux, a new fox will appear in the place of the eaten tux.
* Create a new class called Hunter. Have the Hunter class inherit from the Creature class and put one hunter into your Game.
* Make the hunter eat both fox and tux when it is close to them.
* (Challenge) Could you make the hunter move differently than the Fox and the Tux? For example, the hunter can just patrol back and forth in the middle of the room along the x axis?

