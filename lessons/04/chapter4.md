---
title: ArrayList
---
Before starting this lesson, you should know

* How to create an ArrayList object
* How to use the add() and get() method of an ArrayList object
* How to use the for loop to process all elements in an ArrayList object

# Refactoring with ArrayList

Now that we have covered ArrayList, let's see how it can be used to improve on the structure of our previous assignment. The act of improving the structure on some previously written code without added new features is called "refactoring".

First, look at the 3 classes in the "code listing" section. It contains a solution to the Doty feeding exercise. Study it carefully. You will noticed that some sections of the code are very similar. The code is also very inflexible since adding an extra food would require changing many different sections of the code (if you don't believe me, try adding a new food to this program!)

Using ArrayList, refactor the program to eliminate the repeated code, and increase flexibility so that adding a new food requires very little change to your code. As a hint, your first job is to replace the 3 Food objects with the following line:

    private ArrayList<Food> foods;

# Code Listing

Game class

```java
import env3d.Env;

/**
 * 
 * The purpose of this exercise is the give you some
 * practice on ArrayList.  
 * 
 * The following doty eating game contains lots of repeated
 * code. You can elimiate them by using ArrayList.
 * 
 */
public class Game
{
    private Doty doty1;
    private Food food1, food2, food3;
    private Env env;    
    private boolean finished;
    
    /**
     * Constructor for the Game class. It sets up doty and food.
     */
    public Game()
    {
        doty1 = new Doty(5,1,1);
        
        // Creating the foods
        food1 = new Food(1,1,1);
        food2 = new Food(2,1,3);
        food3 = new Food(7,1,5);
        
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
        
        // Adding doty to the environment
        env.addObject(doty1);
                
        // Adding food to the environment
        env.addObject(food1);
        env.addObject(food2);
        env.addObject(food3);        
                
        // Sets up the camera
        env.setCameraXYZ(5,5,13);
        env.setCameraPitch(-25);
        
        // Turn off the default controls
        env.setDefaultControl(false);
        
        
        // The main game loop
        while (finished == false)
        {
            // User input
            checkInput();
            
            // Update game objects
            checkFoods();
            checkWin();                          

            // Update display
            env.advanceOneFrame();
        }
        
        // Just a little clean up
        env.exit();
    }
    
    /**
     * Checks the user input
     */
    private void checkInput()
    {
        int currentKey = env.getKey();
            
        if (currentKey == 1) {
            finished = true;
        } else if (currentKey == 200) {
            // Up arrow
            doty1.moveZ(-1);
        } else if (currentKey == 208) {
            // Down arrow
            doty1.moveZ(1);
        } else if (currentKey == 203) {
            // Left arrow
            doty1.moveX(-1);                
        } else if (currentKey == 205) {
            // Right arrow
            doty1.moveX(1);
        }        
    }
    
    /**
     * Check to see if the user has won
     */
    private void checkWin()
    {
        // Make doty smile when all foods are eaten
        if (food1.isDisappeared() == true && food2.isDisappeared() == true && food3.isDisappeared() == true) {
            doty1.smile();
        }        
    }
    
    /**
     * Check to see if any food has been eaten
     */
    private void checkFoods()
    {
        // Checks to see if doty1 is close to the first food
        double dist1 = distance(doty1.getX(), food1.getX(), doty1.getY(), food1.getY(), doty1.getZ(), food1.getZ());
        if (dist1 < food1.getScale()+doty1.getScale()) {
            food1.disappear();
            doty1.eat();
        }
            
        // Checks to see if doty1 is close to the second food
        double dist2 = distance(doty1.getX(), food2.getX(), doty1.getY(), food2.getY(), doty1.getZ(), food2.getZ());
        if (dist2 < food2.getScale()+doty1.getScale()) {
            food2.disappear();
            doty1.eat();              
        }
            
        // Checks to see if doty1 is close to the third food            
        double dist3 = distance(doty1.getX(), food3.getX(), doty1.getY(), food3.getY(), doty1.getZ(), food3.getZ());
        if (dist3 < food3.getScale()+doty1.getScale()) {
            food3.disappear();
            doty1.eat();           
        }        
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
}
```

Doty class

```java
/**
 * The Doty class models a creature that shrinks every
 * time it moves.  It can also eat food to grow.
 * 
 */
public class Doty 
{

    private double x;
    private double y;
    private double z;
    private String texture;
    private double scale;    
    private double rotateY;    
    
    private double moveFactor;
    private double growFactor;
    
    /**
     * Constructor for objects of class Doty
     * 
     * @param x the x location
     * @param y the y location
     * @param z the z location
     * 
     */
    public Doty(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        texture = "textures/doty.gif";
        scale = 1;
        rotateY = 0;
        
        moveFactor = -0.05;
        growFactor = 0.2;
    }
    
    /**
     * Move Doty in the x direction.
     * 
     * @param dir 1 moves doty in the positive direction, -1 moves doty in the negative direction
     */
    public void moveX(int dir)
    {
        // Make Doty face the right direction
        if (dir > 0) {
            rotateY = 90;
        } else {
            rotateY = -90;
        }            
            
        x = x + scale*dir;
        grow(moveFactor);
    }
    
    /**
     * Move Doty in the z direction.
     * 
     * @param dir 1 moves doty in the positive direction, -1 moves doty in the negative direction
     */    
    public void moveZ(int dir)
    {
        if (dir > 0) {
            rotateY = 0;
        } else {
            rotateY = 180;
        }
        z = z + scale*dir;
        grow(moveFactor);        
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
     * Change doty scale by a certain amount
     * 
     * @param delta the amount scale will be added to
     */
    public void grow(double delta)
    {
        scale = scale + delta;
    }    

    /**
     * Change doty after eating a food
     */
    public void eat()
    {
        grow(growFactor);
    }
    
    /**
     * Make doty smile
     */
    public void smile()
    {
        texture = "textures/doty_happy.gif";
    }
}
```

Food class

```
/**
 * A object of the food class represents a
 * good in the game.  It can be eaten (disappeared).
 * 
 */
public class Food 
{

    private double x, y, z;
    private double scale;

    
    /**
     * Constructor for objects of class Food
     */
    public Food(double x, double y, double z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 0.3;
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
     * Make doty disappear
     */
    public void disappear()
    {
        x = 100;
    }
    
    /**
     * Return if this food has disappeared
     * 
     * @return true is the food has disappeared, false otherwise
     */
    public boolean isDisappeared()
    {
        if (x >= 100) {
            return true;
        } else {
            return false;
        }
    }
}
```
 
