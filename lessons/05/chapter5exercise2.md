---
title: World of Zuul v.2
---
 This is a better version of the World of Zuul 3D game. After completing the set exercises, try adding to the game with your own ideas.

The design of this program is different from version 1. It contains many enhancements and improvements. The most notable changes are:

* The Room class is using a HashMap to keep track of exits.
* The Doty class has a "revert" method.
* The "checkWall" method inside the Game class has changed completely.

# Step 1

Explain how the above changes are beneficial to the structure of our Game. In particular, explain how each change allows for greater flexibility.

# Step 2

Right now, you can only create rooms to the east and west. Incorporate the north and south wall in this new design. Create a 5 room setup just like in version 1.

# Step 3

In this version, we can create objects of the Block class in each Room. Doty cannot walk pass a block. Each block contains a description and the player can look at the description of each block by pressing the 'L' key when Doty is near a block. Modify your program so that if Doty is near a "Breakable Block", and the user presses the 'B' key, the breakable block will disappear. The 'B' key will have no effect on "Normal Block".

# Step 4

Create a new Character class. A character contains a description, and a collection of responses. All of these are of the String type. Modify the room class so that each room may contain 0 or more characters. The Character class must have the following functionality:

* When Doty is close to a character and the user press the 'L' key, the character description will be displayed on screen.
* When Doty is close to a character and the user press the 'T' key, one of the character's responses will be randomly displayed on screen.

# Code Listing

Game class

```java
import java.util.ArrayList;
import env3d.Env;
import org.lwjgl.input.Keyboard;

/**
 * 
 * The Game class.  This class is the entry point
 * of the program and contains a controller loop.
 * 
 */
public class Game 
{
    private boolean finished;
    private Doty doty1;
    private Env env;
    private Room roomLeft, roomRight;
    private Room currentRoom;
    
    
    /**
     * The constructor.  It sets up all the rooms and necessary 
     * objects in each room.
     * 
     */
    public Game()
    {
        roomLeft = new Room(10, 13.001, 10, "The left room");
        roomRight = new Room(10, 13.001, 10, "The right room");
        
        roomLeft.setExit("east", roomRight);
        roomLeft.setTextureEast("textures/door.gif");
        roomLeft.addBlock(new Block(1, 1, 4, "Normal Block"));
        roomLeft.addBlock(new Block(4, 1, 8, "Normal Block"));
        roomLeft.addBlock(new Block(7, 1, 3, "Normal Block"));
        
        roomRight.setExit("west", roomLeft);
        roomRight.setTextureWest("textures/door.gif");                
        roomRight.addBlock(new Block(3, 1, 4, "Normal Block"));
        roomRight.addBlock(new Block(5, 1, 2, "Breakable Block"));
        roomRight.addBlock(new Block(6, 1, 8, "Normal Block"));        
        
        doty1 = new Doty(5, 1, 1);
        
    }
    
    
    /**
     * The play method contains the controller loop.
     */
    public void play()
    {
        finished = false;
        
        // Create a new environment
        env = new Env();
        
        // Starts with roomLeft
        setCurrentRoom(roomLeft);        
        // Adding doty to the environment
        doty1.setRoomDim(currentRoom.getWidth(), currentRoom.getDepth());        
        env.addObject(doty1);
                
        // Position the camera
        env.setCameraXYZ(5, 13, 9);        
        env.setCameraPitch(-75);
        
        // Disable mouse and camera control
        env.setDefaultControl(false);
        
        // The "loop"
        while (finished == false)
        {            
            // Ask for user input
            processInput();
            
            // Process the various objects
            checkWall();            
            checkCollision();
            
            // Update the screen
            env.advanceOneFrame();
        }
        
        // Just a little cleanup
        env.exit();
    }
    
    /**
     * Helper method for setting the current room to a 
     * particular room.
     * 
     * @param room The room to set the current room to.
     */
    private void setCurrentRoom(Room room)
    {
        if (room != null) {
            currentRoom = room;
            env.setRoom(currentRoom);
            for (Block block : currentRoom.getBlocks()) {
                env.addObject(block);
            }
        }
    }
    
    /**
     * Process the user input
     * 
     */
    private void processInput() 
    {        
        // The getKeyDown() method can detect which
        // button is being held down.  Can only detect
        // one button.
        int currentKey = env.getKeyDown();
        
        // Reset the display string
        if (currentKey != 0) {
            env.setDisplayStr("");
        }
                
        // Note the use of class constants.  Much more readable!
        if (currentKey == Keyboard.KEY_ESCAPE) {
            finished = true;
        } else if (currentKey == Keyboard.KEY_UP) {
            doty1.moveZ(-0.05);
        } else if (currentKey == Keyboard.KEY_DOWN) {
            doty1.moveZ(0.05);
        } else if (currentKey == Keyboard.KEY_LEFT) {
            doty1.moveX(-0.05);
        } else if (currentKey == Keyboard.KEY_RIGHT) {
            doty1.moveX(0.05);
        } else if (currentKey == Keyboard.KEY_L) {
            // This is the "L"ook command.  So we display the description of the room.
            env.setDisplayStr(currentRoom.getDescription());
            
            // If Doty is close to an object, display the description of the object instead.
            for (Block block : currentRoom.getBlocks()) {
                double dist = distance(doty1.getX(), block.getX(), doty1.getY(), block.getY(), doty1.getZ(), block.getZ());
                if (dist <= doty1.getScale()*1.2+block.getScale()) {
                    env.setDisplayStr(block.getDesc());
                }
            }
        }         
    }
    
    /**
     * Check to see if Doty is close to a wall, and exit to the next room if necessary
     */
    private void checkWall()
    {
        if (doty1.getX() > currentRoom.getWidth()-doty1.getScale()) {
            // Going east
            exitTo("east");
        } else if (doty1.getX() < doty1.getScale()) {
            // Going west
            exitTo("west");
        }        
    }
    
    /**
     * A helper method to reduce duplicated code in checkWall
     */
    private void exitTo(String dir)
    {
        if (currentRoom.getExit(dir) != null) {                
            setCurrentRoom(currentRoom.getExit(dir));
            doty1.setRoomDim(currentRoom.getWidth(), currentRoom.getDepth());
            doty1.setExitFrom(dir);           
            env.addObject(doty1);
        } else {
            // Doty has hit a wall
            doty1.revert();
        }
    }
    
    /**
     * Check to see if any collision occur between Doty and the objects in the current room
     */    
    private void checkCollision()
    {
        // For every wall in the current room
        for (Block block : currentRoom.getBlocks()) {
            // Stop doty from moving if doty hits a wall
            double dist = distance(block.getX(), doty1.getX(), block.getY(), doty1.getY(), block.getZ(), doty1.getZ());
            if (dist <= block.getScale()+doty1.getScale()) {                
                doty1.revert();
            }
        }

    }
    
    /**
     * The private distance method
     */
    private double distance(double x1, double x2, double y1, double y2, double z1, double z2) {
        double xdiff, ydiff, zdiff;
        xdiff = x2 - x1;
        ydiff = y2 - y1;
        zdiff = z2 - z1;
        return (double) Math.sqrt(xdiff*xdiff + ydiff*ydiff + zdiff*zdiff);
    }    
    
    public static void main (String [] args) {
        (new Game()).play();
    }
}
```

Doty class

```java
/**
 * Doty is the main character of the game
 * 
 */
public class Doty
{
    // Doty's location
    private double x, y, z;
    
    // Doty's previous location.  Useful in the revert method
    private double prev_x, prev_y, prev_z;
    
    private String texture;
    private double scale;    
    private double rotateY;
    
    // If doty remembers the room's dimension, doty will be able
    // to set itself to the right position when entering a new
    // room
    private double roomWidth, roomDepth;
    
    
    /**
     * Constructor for objects of class Doty
     */
    public Doty(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        
        this.prev_x = x;
        this.prev_y = y;
        this.prev_z = z;
        
        texture = "textures/doty.gif";
        scale = 1;
        rotateY = 0;
    }
    
    /**
     * Set the dimension of the room Doty is in
     */
    public void setRoomDim(double w, double d)
    {
        roomWidth = w;
        roomDepth = d;
    }

    /**
     * This method is called when Doty exits from a room.
     * If Doty exits from the east, then set Doty's position to the
     * west side of the new room.
     * 
     * @param dir The direction in which Doty exited from.
     */
    public void setExitFrom(String dir) 
    {
        if (dir.equals("east")) {
            setX(scale);
        } else if (dir.equals("west")) {
            setX(roomWidth-scale);
        }
    }
    
    /**
     * Move doty in the x direction.
     */
    public void moveX(double delta)
    {
        // Make Doty face the right direction
        if (delta > 0) {
            rotateY = 90;
        } else {
            rotateY = -90;
        }            
        
        // Remember the previous position
        prev_z = z;
        prev_x = x;    
        
        // Move doty
        x = x + delta;
    }
    
    /**
     * Move doty in the z direction
     */
    public void moveZ(double delta)
    {
        if (delta > 0) {
            rotateY = 0;
        } else {
            rotateY = 180;
        }
        
        // Remember the previous position
        prev_z = z;
        prev_x = x;
        
        // Move doty
        z = z + delta;
    }
    
    /**
     * Reverts doty back to the previous position
     */
    public void revert()
    {
        x = prev_x;
        z = prev_z;
    }
    
    public double getX()
    {
        return x;
    }

    public double getY()
    {
        return y;
    }

    public double getZ()
    {
        return z;
    }
    
    public double getScale()
    {
        return scale;
    }
    
    public void setX(double x)
    {
        this.x = x;
    }

    public void setY(double y)
    {
        this.y = y;
    }
    
    public void setZ(double z)
    {
        this.z = z;
    }
}
```

Room class

```java
import java.util.ArrayList;
import java.util.HashMap;

/**
 * A room describes the current environment.  Only one
 * room can be displayed at one time.
 * 
 */
public class Room
{
    // Must have these fields for Env to reconize this room
    private double width, height, depth;
    private String textureNorth, textureSouth, textureEast, textureWest;

    // Some additional fields for the game
    private String description;
    private ArrayList<Block> blocks;
    private HashMap<String, Room> exits;
        
    
    /**
     * Constructor for objects of class Room
     */
    public Room(double w, double h, double d, String desc)
    {
        width = w;
        height = h;
        depth = d;
        description = desc;
        
        textureNorth = "textures/fence0.jpg"; 
        textureEast = "textures/fence0.jpg"; 
        textureSouth = "textures/fence0.jpg"; 
        textureWest =  "textures/fence0.jpg"; 
        
        blocks = new ArrayList<Block>();
        exits = new HashMap<String, Room>();
    }
    
    /**
     * Add a block to this room.
     * 
     * @param block The block object to be added to the room
     */
    public void addBlock(Block block)
    {
        blocks.add(block);
    }
    
    /**
     * Get the block 
     * 
     * @return The collection of blocks
     */
    public ArrayList<Block> getBlocks()
    {
        return blocks;
    }
    
    /**
     * Get the description of the room
     * 
     * @return The description string
     */
    public String getDescription()
    {
        return description;
    }
    
    /**
     * Mutator for the wall texture
     */
    public void setTextureNorth(String fileName)
    {
        textureNorth = fileName;
    }

    /**
     * Mutator for the wall texture
     */
    public void setTextureEast(String fileName)
    {
        textureEast = fileName;
    }

    /**
     * Mutator for the wall texture
     */
    public void setTextureSouth(String fileName)
    {
        textureSouth = fileName;
    }

    /**
     * Mutator for the wall texture
     */
    public void setTextureWest(String fileName)
    {
        textureWest = fileName;
    }
        
    /**
     * Create an exit to a room
     * 
     * @param direction the direction of the exit
     * @param room the room that this direction exits to
     */
    public void setExit(String direction, Room room)
    {
        exits.put(direction, room);
    }
    
    /**
     * Get the room a direction exits to.  
     * 
     * @param direction a string indicating a direction
     * @return the room that the direction exits to.  null if no exit
     * in that direction
     */
    public Room getExit(String direction)
    {
        return exits.get(direction);
    }
    
    /**
     * Accessor for the room's dimension
     */
    public double getWidth()
    {
        return width;
    }
    
    /**
     * Accessor for room's dimension
     */
    public double getDepth()
    {
        return depth;
    }
}
```

Block class

```java
/**
 * A Block prevents the player from moving.
 * 
 */
public class Block
{

    private double x, y, z;
    private double scale;
    private String desc;
    
    /**
     * Constructor for objects of class Block
     */
    public Block(double x, double y, double z, String desc)
    {
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 0.5;
        this.desc = desc;
    }
    
    /**
     * Make the block disappear
     */
    public void disappear()
    {
        x = 100;
    }
    
    /**
     * Accessor for the block's description
     */
    public String getDesc()    
    {
        return desc;
    }

    /**
     * Accessor for the block's location
     */
    public double getX()
    {
        return x;
    }

    /**
     * Accessor for the block's location
     */
    public double getY()
    {
        return y;
    }

    /**
     * Accessor for the block's location
     */
    public double getZ()
    {
        return z;
    }
    
    /**
     * Accessor for the block's size
     */
    public double getScale()
    {
        return scale;
    }    
}
```
