---
title: Fox vs Tux Exercise
---

Note: the  complete code listing of our simulation program can be found at the bottom of this page.

# Exercise 1
The movement of all of our creatures faces south and move in large steps.  Make each creature turn to face the direction it is moving, and also make the movement more "smooth".  Your result should look like the following video.
<iframe width="560" height="315" src="https://www.youtube.com/embed/qQSq34S00uk?rel=0" frameborder="0" allowfullscreen></iframe>

# Exercise 2
Further mofiy your creatures so that they can randomly turn to any angle.
<iframe width="560" height="315" src="https://www.youtube.com/embed/MDA_XATAYio?rel=0" frameborder="0" allowfullscreen></iframe>

# Exercise 3
Make the fox class more intelligent -- have it seek out the tux that is closest to it. (HINT: you'll need to create a move() method in the Fox class the overrides the Creature's move() method.)
<iframe width="560" height="315" src="https://www.youtube.com/embed/YuXG5Vg8H54?rel=0" frameborder="0" allowfullscreen></iframe>

# Exercise 4
Put some obstacles in your simulation, similar to "blocks" from the world of zuul project.

# Exercise 5
(Project) Add additional creatures and more complex behaviors.

--- 

# The Creature class

```java
import env3d.EnvObject;
import java.util.ArrayList;
 
abstract public class Creature extends EnvObject
{
     
    /**
     * Constructor for objects of class Creature
     */
    public Creature(double x, double y, double z)
    {
        setX(x);
        setY(y);
        setZ(z);
        setScale(1);
    }
     
    public void move(ArrayList<Creature> creatures, ArrayList<Creature> dead_creatures)
    {                
        double rand = Math.random();
        if (rand < 0.25) {
            setX(getX()+getScale());
        } else if (rand < 0.5) {
            setX(getX()-getScale());
        } else if (rand < 0.75) {
            setZ(getZ()+getScale());
        } else if (rand < 1) {
            setZ(getZ()-getScale());
        }                
         
        if (getX() < getScale()) setX(getScale());
        if (getX() > 50-getScale()) setX(50 - getScale());
        if (getZ() < getScale()) setZ(getScale());
        if (getZ() > 50-getScale()) setZ(50 - getScale());
  
        // The move method now handles collision detection
        if (this instanceof Fox) {
            for (Creature c : creatures) {
                if (c.distance(this) < c.getScale()+this.getScale() && c instanceof Tux) {
                    dead_creatures.add(c);
                }
            }
        }
    }        
}
```

# The Fox and Tux classes

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

# The Game class

```java
import env3d.Env;
import java.util.ArrayList;
 
/**
 * A predator and prey simulation.  Fox is the predator and Tux is the prey.
 */
public class Game
{
    private Env env;    
    private boolean finished;
     
    private ArrayList<Creature> creatures;
 
    /**
     * Constructor for the Game class. It sets up the foxes and tuxes.
     */
    public Game()
    {
        // we use a separate ArrayList to keep track of each animal. 
        // our room is 50 x 50.
        creatures = new ArrayList<Creature>();
        for (int i = 0; i < 55; i++) {
            if (i < 5) {
                creatures.add(new Fox((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));        
            } else {
                creatures.add(new Tux((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));
            }
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
        for (Creature c : creatures) {
            env.addObject(c);
        }
         
        // Sets up the camera
        env.setCameraXYZ(25, 50, 55);
        env.setCameraPitch(-63);
         
        // Turn off the default controls
        env.setDefaultControl(false);
         
        // A list to keep track of dead tuxes.
        ArrayList<Creature> dead_creatures = new ArrayList<Creature>();
         
        // The main game loop
        while (!finished) {            
         
            if (env.getKey() == 1)  {
                finished = true;
             }
         
            // Move each fox and tux.
            for (Creature c : creatures) {
                c.move(creatures, dead_creatures);
            }
             
            // Clean up of the dead tuxes.
            for (Creature c : dead_creatures) {
                env.removeObject(c);
                creatures.remove(c);
            }
            // we clear the ArrayList for the next loop.  We could create a new one 
            // every loop but that would be very inefficient.
            dead_creatures.clear();
         
            // Update display
            env.advanceOneFrame();
        }
         
        // Just a little clean up
        env.exit();
    }
     
     
    /**
     * Main method to launch the program.
     */
    public static void main(String args[]) {
        (new Game()).play();
    }
}
```

# The Room class

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
        textureBottom = "textures/marble.gif";
    }
}
```
 
