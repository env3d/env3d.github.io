---
title: World of Zuul v.1
---
Preamble: This project is designed to show you how badly the code is written, with code duplication in many places.

This project contains the basic framework for a "Multi-room Doty" game. Notice that Doty is now able to move from room to room. The current code starts with only 2 rooms.

Start with the template project and add all the classes from the "Code Listing" section.
Step 1

Add food into the game. Add an Arraylist<Food> foods to the Room class. Also, create a method void addFood(Food f) in the room class. The purpose of this method is give the caller the ability to add foods to each room.

In the constructor of the game class, you can setup your game by adding foods to each room:

```java
    public Game()
    {
        roomLeft = new Room(10, 15, 10, "The left room");
        roomRight = new Room(10, 15, 10, "The right room");

        // Setup the left room
        roomLeft.setExits(null, roomRight, null, null);
        roomLeft.setTextureEast("textures/door.gif");
        roomLeft.addFood(new Food(1, 1, 4));
        roomLeft.addFood(new Food(4, 1, 8));
        roomLeft.addFood(new Food(7, 1, 3));

        // Setup the right room
        roomRight.setExits(null, null, null, roomLeft);
        roomRight.setTextureWest("textures/door.gif");
        roomRight.addFood(new Food(2, 1, 4));
        roomRight.addFood(new Food(5, 1, 3));
        roomRight.addFood(new Food(6, 1, 3));

        doty1 = new Doty(5, 1, 1);
    }
```

Every time Doty moves to a different room, make sure that all the foods are added back to the environment.
Step 2

We want Doty to be able to eat up the foods. Add a private method void checkFood(). This method will be called every frame. The purpose of this method is to check if Doty is close to any of the foods in the current room. Make the food disappear if Doty is close to any of the foods.
Step 3

Right now, each room can only have 2 exits. Doty can also move out of the wall on the north and south directions. Modify the Room class as well as the checkWall() method so that each room can now have 4 exits (north, east, south, west).

Create a map of 5 rooms as show below:

```
              [ North Room  ]
[ West Room ] [ Middle Room ] [ East Room ]
              [ South Room  ]
```

Start Doty in the middle room. Put foods in all of the rooms. End the game when all foods are eaten.
Code Listing

Game class

```java
import java.util.ArrayList;
import env3d.Env;

/**
 * This is the main Game class.  It is the main entry point of our game and contains the control 
 * loop and all the various support methods.  
 * 
 * Only the play method is meant to be called by the caller to start the game.
 * 
 */
public class Game
{
    private boolean finished;
    private Doty doty;
    private Env env;
    private Room roomLeft, roomRight;
    private Room currentRoom;
    
    /**
     * The constructor of the game.  It sets up all the room information 
     * and the doty's position.
     */
    public Game()
    {
        roomLeft = new Room(10, 15, 10);
        roomRight = new Room(10, 15, 10);
        
        roomLeft.setExits(null, roomRight);
        roomLeft.setTextureEast("textures/door.gif");
        
        roomRight.setExits(roomLeft, null);
        roomRight.setTextureWest("textures/door.gif");               
        
        doty = new Doty(5, 1, 1);
    }
    
    /**
     * This is the main game loop.  Try to keep it as short
     * as possible.
     */
    public void play()
    {
        finished = false;
        
        // Create a new environment
        env = new Env();
        
        // Starts with roomLeft
        currentRoom = roomLeft;
        env.setRoom(currentRoom);
        
        // Adding doty to the environment
        env.addObject(doty);
                
        // Position the camera
        env.setCameraXYZ(5, 14, 9);
        env.setCameraPitch(-75);
        
        // Disable mouse and camera control
        env.setDefaultControl(false);
        
        while (finished == false)
        {            
            processInput();

            checkWall();
            
            env.advanceOneFrame();
        }
        
        env.exit();
    }    
    
    /**
     * Checks keyboard input
     */
    private void processInput() 
    {
        int currentKey = env.getKey();
            
        if (currentKey == 1) {
            finished = true;
        } else if (currentKey == 200) {
            // Up arrow
            doty.moveZ(-1);
        } else if (currentKey == 208) {
            // Down arrow
            doty.moveZ(1);   
        } else if (currentKey == 203) {
            // Left arrow
            doty.moveX(-1);            
        } else if (currentKey == 205) {
            // Right arrow
            doty.moveX(1);         
        } 
    }
    
    /**
     * Check to see if doty has hit a wall.  It then tries to determine
     * if doty should be moved to the next room.
     */
    private void checkWall()
    {
        if (doty.getX() > currentRoom.getWidth()-doty.getScale()) {
            // Going east
            if (currentRoom.getEastExit() != null) {                
                currentRoom = currentRoom.getEastExit();
                env.setRoom(currentRoom);
                doty.setX(doty.getScale());                    
                env.addObject(doty);
            } else {
                // Doty has hit a wall
                doty.setX(currentRoom.getWidth()-doty.getScale());
            }
        } else if (doty.getX() < doty.getScale()) {
            // Going west
            if (currentRoom.getWestExit() != null) {
                currentRoom = currentRoom.getWestExit();
                env.setRoom(currentRoom);
                doty.setX(currentRoom.getWidth()-doty.getScale());                    
                env.addObject(doty);                    
            } else {
                // Doty has hit a wall
                doty.setX(doty.getScale());
            }
        }        
    }
    
    /**
     * Basic distance method.  It calculates the distance between 2 objects given
     * 2 sets of x, y, and z coordinates.
     */
    private double distance(double x1, double x2, double y1, double y2, double z1, double z2) {
        double xdiff, ydiff, zdiff;
        xdiff = x2 - x1;
        ydiff = y2 - y1;
        zdiff = z2 - z1;
        return (double) Math.sqrt(xdiff*xdiff + ydiff*ydiff + zdiff*zdiff);
    }
}
```

Doty class

```java
/**
 * Doty is the main character of our game.  For
 * now, it can only move around.
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
    
    
    /**
     * Constructor for objects of class Doty.  The
     * caller can create a doty in a flexible location
     * 
     * @param x the x coordinate
     * @param y the y coordinate
     * @param z the z coordinate
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
    }
    
    /**
     * Moves doty in the x direction
     * 
     * @param dir the direction. 
     * 1 means positive (to the right), 
     * -1 means negative (to the left), 
     * 0 means don't move
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
    }
    
    /**
     * Moves doty in the z direction
     * 
     * @param dir the direction. 
     * 1 means positive (towards the screen), 
     * -1 means negative (away from the screen), 
     * 0 means don't move
     */
    public void moveZ(int dir)
    {
        if (dir > 0) {
            rotateY = 0;
        } else {
            rotateY = 180;
        }
        z = z + scale*dir;
    }
    
    /**
     * Returns the x coordinate
     */
    public double getX()
    {
        return x;
    }

    /**
     * Returns the y coordinate
     */
    public double getY()
    {
        return y;
    }

    /**
     * Returns the z coordinate
     */
    public double getZ()
    {
        return z;
    }
    
    /**
     * Returns the current scale of doty
     */
    public double getScale()
    {
        return scale;
    }
    
    /**
     * Sets the x coordinate
     * 
     * @param x the x coordinate
     */
    public void setX(double x)
    {
        this.x = x;
    }

    /**
     * Sets the y coordinate
     * 
     * @param y the y coordinate
     */
    public void setY(double y)
    {
        this.y = y;
    }
    
    /**
     * Sets the z coordinate
     * 
     * @param z the z coordinate
     */
    public void setZ(double z)
    {
        this.z = z;
    }
    
    /**
     * Grow doty by delta amount
     * 
     * @param delta the amount by which doty will grow
     */
    public void grow(double delta)
    {
        scale = scale + delta;
    }
    
    /**
     * Switch doty's texture to a simily face
     */
    public void smile()
    {
        texture = "textures/doty_happy.gif";
    }
}
```

Room class

```java
import java.util.ArrayList;

/**
 * The Room class represents a generic room.  
 * The dimensions of the room is flexible
 * 
 */
public class Room
{
    // instance variables - replace the example below with your own
    private double width, height, depth;
    private String textureNorth, textureSouth, textureEast, textureWest;
    private Room exitEast, exitWest;
        
    
    /**
     * Constructor for objects of class Room.  Allows the 
     * caller to create rooms of different dimensions.
     */
    public Room(double w, double h, double d)
    {
        width = w;
        height = h;
        depth = d;
        
        textureNorth = "textures/fence0.jpg"; 
        textureEast = "textures/fence0.jpg"; 
        textureSouth = "textures/fence0.jpg"; 
        textureWest =  "textures/fence0.jpg"; 
        
    }
    
    /**
     * Sets the north texture of the room
     * 
     * @param fileName name of the texture file
     */
    public void setTextureNorth(String fileName)
    {
        textureNorth = fileName;
    }

    /**
     * Sets the east texture of the room
     * 
     * @param fileName name of the texture file
     */
    public void setTextureEast(String fileName)
    {
        textureEast = fileName;
    }

    /**
     * Sets the south texture of the room
     * @param fileName name of the texture file
     */
    public void setTextureSouth(String fileName)
    {
        textureSouth = fileName;
    }

    /**
     * Sets the west texture of the room
     * @param fileName name of the texture file
     */
    public void setTextureWest(String fileName)
    {
        textureWest = fileName;
    }
    
    /**
     * Every object of the room class can have 2 exits.
     * Each exit is actually a pointer to another room.
     * We can essentially create a long corridor.
     * 
     * @param west The west exit 
     * @param east The east exit
     */
    public void setExits(Room west, Room east)
    {
        exitEast = east;
        exitWest = west;
    }
    
    /**
     * Returns the east exit room to the caller
     */
    public Room getEastExit()
    {
        return exitEast;
    }
    
    /**
     * Returns the west exit room to the caller
     */
    public Room getWestExit()
    {
        return exitWest;        
    }

    /**
     * Returns the width of the room
     */
    public double getWidth()
    {
        return width;
    }
    
    /**
     * Returns the depth of the room
     */
    public double getDepth()
    {
        return depth;
    }   
}
```

Food class

```java
/**
 * Represents a food in the game
 */
public class Food
{

    private double x, y, z;
    private double scale;
    
    /**
     * Constructor for objects of class Food.  This
     * is flexible and the caller can position food anywhere
     */
    public Food(double x, double y, double z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 0.3;
    }

    /**
     * Get the x coordinate of this food
     */
    public double getX()
    {
        return x;
    }

    /**
     * Get the y coordinate of this food
     */
    public double getY()
    {
        return y;
    }

    /**
     * Get the z coordinate of this food
     */
    public double getZ()
    {
        return z;
    }

    /**
     * Get the scale of this food
     */
    public double getScale()
    {
        return scale;
    }
    
    /**
     * Make the food disappear.  It simply sets the food to be out of sight.
     */
    public void disappear()
    {
        x = 100;
    }
}
```
