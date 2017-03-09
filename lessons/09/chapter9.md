---
title: A Better Design
---

Maintainability and extensibility are two major reasons why we want our software to be well structured. In this lesson, we are going to look at some additional techniques to further improve our simulation software.
The EnvObject class

All objects that are displayable inside env3d have many fields in common. In previous lessons, we created a Creature class to hold the common fields as well as mutators and accessors to those fields for convenience.

The env3d package actually includes a EnvObject class that contains all of the common fields and methods for env3d objects. Check out the javadoc at http://env3d.sourceforge.net/javadoc/env3d/EnvObject.html.

We can make our Creature class much simpler by inheriting from the EnvObject class, as follows:

```java
import env3d.EnvObject;

public class Creature extends EnvObject
{
    
    /**
     * Constructor, need to provide a location
     */
    public Creature(double x, double y, double z)
    {
        setX(x);
        setY(y);
        setZ(z);
        setScale(1);        
    }
    
    /**
     * Move the creature randomly
     */
    public void move() 
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
    }
}
```

Since all of the common parts of env3d objects are already included in the EnvObject class, we can just inherit from it to avoid rewriting our code.

# Simulation Logic

Currently, our simulation logic resides inside the main game loop. As we add more creatures and more logic into our simulation, our simulation logic would get bigger and bigger. Also, whenever we add a new creature into our simulation, we will also need to modify our Game class as well. Wouldn't it be nice to have the simulation logic embedded inside each of the creature class so that when we add a new creature into our simulation, we only need to modify one class (i.e. the new creature class itself).

To make this possible, we need to change the structure of our simulation logic code.

Our current simulation logic code looks like this:

```java
            // Move each fox and tux.
            for (Creature c : creatures) {
                c.move();
            }
                        
            // Simulation logic, if a fox touches a tux, the tux will be dead.
            for (Creature c1 : creatures) {
                for (Creature c2 : creatures) {                    
                    if (distance(c1.getX(), c2.getX(), c1.getY(), c2.getY(), c1.getZ(), c2.getZ()) < c1.getScale()+c2.getScale()) {
                        if (c1 instanceof Fox && c2 instanceof Tux) {
                            dead_creatures.add(c2);
                        }
                    }
                }
            }

            
            // Clean up of the dead tuxes.
            for (Creature c : dead_creatures) {
                env.removeObject(c);
                creatures.remove(c);
            }
```

Since we are already using a for loop to move each creature, we could simplify the above code as follows:

```java
            // Move each fox and tux.
            for (Creature c : creatures) {
                c.move();
                for (Creature c2 : creatures) {                    
                    if (distance(c.getX(), c2.getX(), c.getY(), c2.getY(), c.getZ(), c2.getZ()) < c.getScale()+c2.getScale()) {
                        if (c instanceof Fox && c2 instanceof Tux) {
                            dead_creatures.add(c2);
                        }
                    }
                }
            }
                        
            
            // Clean up of the dead tuxes.
            for (Creature c : dead_creatures) {
                env.removeObject(c);
                creatures.remove(c);
            }
```

This is better, but we can make this code even simpler if we put the nested for loop inside the move() method of Creature, so that our Game code can simply be:

```
            // Move each fox and tux.  The creature's move method will handle collision detection
            for (Creature c : creatures) {
                c.move();
            }
                        
            
            // Clean up of the dead tuxes.
            for (Creature c : dead_creatures) {
                env.removeObject(c);
                creatures.remove(c);
            }
```

And the Creature's move() method will look like:

```java
class Creature 
{
    // ...the rest of the class omitted...

    public void move()
    {
        // The move method now handles collision detection
        if (this instanceof Fox) {
            for (Creature c : creatures) {
                if (c.distance(this) < c.getScale()+this.getScale() && c instanceof Tux) {
                    dead_creatures.add(c);
                }
            }
        }
        
    }
```

That looks much better for our game loop, but if you try to compile this code, you'll see that there is an error. The creatures and dead_creatures list are not accessible inside the Creature class, as they are fields in the Game class. A solution is to pass those lists to the move() method using parameters:

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

In our Game class, we modify the call to the move() method like this:

```java
            // Move each fox and tux.  The creature's move method will handle collision detection
            for (Creature c : creatures) {
                c.move(creatures, dead_creatures);
            }
```

A great benefit of this design is that if we want to change the collision detection behavior, we don't have to touch the Game class at all.

# Making each subclass' behavior different

We have made some great improvements to make our Creature class handle collision detection, but right now, the Creature class handles ALL the interaction between Fox and Tux. If we add a new Creature subclass to the simulation, we still have to modify the Creature's move() method to handle the new class. Our goal is to make it easy to add a new creature without modifying existing code (to achieve maximum flexibility). We can do this by moving the collision detection code down to the sub-class level.

First we take out the collision detection code in the Creature class:

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
    }
}
```

Then we modify our Fox class to eat the Tux, like this:

```java
import java.util.ArrayList;

public class Fox extends Creature
{
    private int dir;
    
    public Fox(double x, double y, double z)
    {
        super(x, y, z);
        setTexture("models/fox/fox.png");
        setModel("models/fox/fox.obj");
        dir = 1;
    }
    
    /**
     * This move() method overrides the Creature's move method.
     */
    public void move(ArrayList<Creature> creatures, ArrayList<Creature> dead_creatures)
    {
        // super.move() calls the move() method in the parent's class
        super.move(creatures, dead_creatures);
        
        for (Creature c : creatures) {
            if (c.distance(this) < c.getScale()+this.getScale() && c instanceof Tux) {
                dead_creatures.add(c);
            }
        }
        
    }
}
```

When the Fox's move() method is called, not only does it move in a random direction, it is also performing a collision detection to find if any Tux is close to it. It will add the tux object to the dead_creatures list if a collision occurs.

For reference, our final game loop looks like this:

```java
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
```

Our game loop is much cleaner now! The creature collision is now being handled within the specific creature class itself.

# Exercises

1. Re-implement the following rules from your previous exercise using this new framework.  Choose your value for n, m, and k carefully so that your simulation is stable (will not get too slow or have all the creatures disappear). 
   1. Make the fox die (disappear) if it has not eaten anything after 50 turns.
   1. When a tux is eaten by a fox, n% of the time, create a new fox in place of the dead tux.
   1. When a tux is close to another tux, m% of the time, a new tux is born.
   1. When k or more tuxes are close in proximity, they all get overcrowded and die.
1. Make it so that the user can choose the values for n, m, and k.
1. Add a hunter to our simulation - this should be much easier now.

 

