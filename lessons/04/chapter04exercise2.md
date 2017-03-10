---
title: Monster VS Doty Exercise
---
Copy the 3 classes from the "Code Listing" section to the template project. You'll notice a few things about this program:

* The monster can be controlled by the arrow keys on the keyboard. Make sure you understand how the run animation is called.
* If you press the "S" key, the swipe animation will run and a ball will fly out of the monster.
* You notice also that you only get 1 ball, if you examine the Game class, you'll see that the same Ball object is used over and over again.

# Exercise 1

Your challenge is to create a list of balls in game, instead of just 1 ball. Then, make it possible for the monster to fire multiple balls. The potential balls that can be fired is unlimited.

HINT: For this exercise, do not worry about removing the balls from the list or the environment.

# Exercise 2

Create a Doty class and place a few Doty objects in the room, the constructor for Game looks like this:

```java
public Game()
{ 
    // Initial monster location
    m = new Monster(5,1,1);
    balls = new ArrayList<Ball>();
    dotys = new ArrayList<Doty>();
    dotys.add(new Doty(1, 1, 3));
    dotys.add(new Doty(8, 1, 6));
    dotys.add(new Doty(4, 1, 8));
}
```

Make it so that the balls that the monster fire will be able to eliminate the various dotys.

# Code Listing

Game class

```java
// Import the Env class to work with the 3D Environment 
import env3d.Env;
import org.lwjgl.input.Keyboard;

/**
 * Game class, the main controller class
 */
public class Game
{
    // This game controlls exactly 1 Doty object
    private Monster m;
    private Ball b;
    
    /**
     * Put the doty at default location
     */ 
    public Game()
    { 
        // Initial monster location
        m = new Monster(5,1,1);
        // Initial ball location - hidden
        b = new Ball(100, 0, 0);
    }
    
    /**
     * The play method is the main entry point of the entire 
     * game.  It contains some setup code and a game loop 
     */
    public void play()
    {
        // Initial setup code
        boolean finished;
        finished = false;
        
        Env env;
        // Create the environment.  This next line will cause a 
        // blank window to appear
        env = new Env();
        
        // Add objects to the environment
        env.addObject(m);
        env.addObject(b);        
        
        // Camera control.  We fix the camera and
        // not allow users to change it.
        env.setCameraXYZ(5,5,13);
        env.setCameraPitch(-25);
        env.setDefaultControl(false);
        
        // The main game loop
        while (finished == false)
        {
            // --- Process user input ---
            
            // Keyboard input
            // Track a complete key press (key up)
            int key = env.getKey();
            // Track a key down only
            int keyDown = env.getKeyDown();
            
            if (key == Keyboard.KEY_ESCAPE) {
                finished = true;
            } else if (key == Keyboard.KEY_S) {
                m.swipe();
                b.setDir(m.getRotateY());
                b.setXYZ(m.getX(), m.getY(), m.getZ());                
            } 
            
            // Use key down to move the monster
            if (keyDown == Keyboard.KEY_RIGHT) {
                m.moveX(0.1);
            } else if (keyDown == Keyboard.KEY_LEFT) {
                m.moveX(-0.1);
            } else if (keyDown == Keyboard.KEY_UP) {
                m.moveZ(-0.1);
            } else if (keyDown == Keyboard.KEY_DOWN) {
                m.moveZ(0.1);
            } else if (keyDown == Keyboard.CHAR_NONE) {
                if (m.getState().equals("run")) {
                    m.idle();
                }
            }
            
           
            // --- finish user input ---
            
            // Change the object state
            m.move();
            b.move();
            
            // Update display
            env.advanceOneFrame();
        }

        // Cleanup code
        env.exit();
    }   
}
```

Monster class

```java
import java.util.ArrayList;

/**
 * A monster with various states
 * 
 */
public class Monster
{

    private double x;
    private double y;
    private double z;
    private String texture;
    private String model;
    private double scale;
    private double rotateX;
    private double rotateY;
    private double rotateZ;
    
    // Keeps tract of the current frame
    private int frame;
    
    // Keeps track of the monster's current state
    private String state;
    
    // Lists to keep track of all the models
    private ArrayList<String> idleModels;    
    private ArrayList<String> swipeModels;
    private ArrayList<String> runModels;
    
    /**
     * Constructor for objects of class Monster
     */
    public Monster(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        texture = "models/Boss/boss.jpg";
        scale = 1;
        rotateY = 0;
        
        frame = 0;
        
        // Start state
        state = "idle";
        loadModels();
        model = idleModels.get(0);
    }
    
    /**
     * Load all the models into the proper ArrayList
     */
    public void loadModels() 
    {
        idleModels = new ArrayList<String>();
        idleModels.add("models/Boss/Idle/bossidle00.obj");
        idleModels.add("models/Boss/Idle/bossidle01.obj");
        idleModels.add("models/Boss/Idle/bossidle02.obj");
        idleModels.add("models/Boss/Idle/bossidle03.obj");
        idleModels.add("models/Boss/Idle/bossidle04.obj");
        idleModels.add("models/Boss/Idle/bossidle05.obj");
        idleModels.add("models/Boss/Idle/bossidle06.obj");
        idleModels.add("models/Boss/Idle/bossidle07.obj");
        idleModels.add("models/Boss/Idle/bossidle08.obj");
        idleModels.add("models/Boss/Idle/bossidle09.obj");
        idleModels.add("models/Boss/Idle/bossidle10.obj");
        idleModels.add("models/Boss/Idle/bossidle11.obj");
        idleModels.add("models/Boss/Idle/bossidle12.obj");
        idleModels.add("models/Boss/Idle/bossidle13.obj");
        idleModels.add("models/Boss/Idle/bossidle14.obj");
        idleModels.add("models/Boss/Idle/bossidle15.obj");
        idleModels.add("models/Boss/Idle/bossidle16.obj");
        idleModels.add("models/Boss/Idle/bossidle17.obj");
        idleModels.add("models/Boss/Idle/bossidle18.obj");
        idleModels.add("models/Boss/Idle/bossidle19.obj");
                
        swipeModels = new ArrayList<String>();
        swipeModels.add("models/Boss/Swipe/bossswipe00.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe01.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe02.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe03.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe04.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe05.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe06.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe07.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe08.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe09.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe10.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe11.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe12.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe13.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe14.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe15.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe16.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe17.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe18.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe19.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe20.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe21.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe22.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe23.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe24.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe25.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe26.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe27.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe28.obj");
        swipeModels.add("models/Boss/Swipe/bossswipe29.obj");
        
        runModels = new ArrayList<String>();
        runModels.add("models/Boss/Run/bossrun00.obj");
        runModels.add("models/Boss/Run/bossrun01.obj");
        runModels.add("models/Boss/Run/bossrun02.obj");
        runModels.add("models/Boss/Run/bossrun03.obj");
        runModels.add("models/Boss/Run/bossrun04.obj");
        runModels.add("models/Boss/Run/bossrun05.obj");
        runModels.add("models/Boss/Run/bossrun06.obj");
        runModels.add("models/Boss/Run/bossrun07.obj");
        runModels.add("models/Boss/Run/bossrun08.obj");
        runModels.add("models/Boss/Run/bossrun09.obj");
        runModels.add("models/Boss/Run/bossrun10.obj");
        runModels.add("models/Boss/Run/bossrun11.obj");
        runModels.add("models/Boss/Run/bossrun12.obj");
        runModels.add("models/Boss/Run/bossrun13.obj");
        runModels.add("models/Boss/Run/bossrun14.obj");
        runModels.add("models/Boss/Run/bossrun15.obj");
        runModels.add("models/Boss/Run/bossrun16.obj");
        runModels.add("models/Boss/Run/bossrun17.obj");
        runModels.add("models/Boss/Run/bossrun18.obj");
        runModels.add("models/Boss/Run/bossrun19.obj");
        runModels.add("models/Boss/Run/bossrun20.obj");
        runModels.add("models/Boss/Run/bossrun21.obj");
        runModels.add("models/Boss/Run/bossrun22.obj");
        runModels.add("models/Boss/Run/bossrun23.obj");
        runModels.add("models/Boss/Run/bossrun24.obj");
        runModels.add("models/Boss/Run/bossrun25.obj");
        runModels.add("models/Boss/Run/bossrun26.obj");
        runModels.add("models/Boss/Run/bossrun27.obj");
        runModels.add("models/Boss/Run/bossrun28.obj");
        runModels.add("models/Boss/Run/bossrun29.obj");
        
    }
    
    /**
     * The move method is called every frame.
     * It's purpose is to check the different state and 
     * mutate monster accordingly
     */
    public void move()
    {
        frame++;
        if (state.equals("idle")) {
            idleAction();
        } else if (state.equals("swipe")) {
            swipeAction();
        } else if (state.equals("run")) {
            runAction();
        }
    }
    
    /**
     * Change the state to idle
     */
    public void idle() 
    {
        frame = 0;
        state = "idle";
    }
    
    /**
     * Change the state to swipe
     */
    public void swipe()
    {
        frame = 0;
        state = "swipe";
    }
    
    
    /**
     * Load the correct model when monster is idle
     */
    public void idleAction()
    {

        if (frame < idleModels.size()) {
            model = idleModels.get(frame);
        } else {
            frame = 0;
        }
    }
    
    /**
     * Load the correct model when monster is swiping
     */
    public void swipeAction()
    {
        if (frame < swipeModels.size()) {
            model = swipeModels.get(frame);            
        } else {
            idle();
        }
    }
    
    /**
     * Load the correct model when monster is running
     */
    public void runAction()
    {
        if (frame < runModels.size()) {            
            model = runModels.get(frame);
        } else {
            idle();
        }        
    }    
    
    /**
     * Move monster in the x direction, change model as needed
     */
    public void moveX(double delta)
    {
        if (!state.equals("run")) {
            state = "run";
            frame = 0;
        }

        x += delta;
        if (delta < 0) {
            rotateY = -90;
        } else {
            rotateY = 90;
        }
    }
    
    /**
     * Move monster in the z direction, change model as needed
     */
    public void moveZ(double delta) 
    {
        if (!state.equals("run")) {
            state = "run";
            frame = 0;
        }        
        z += delta;
        if (delta < 0) {
            rotateY = 180;
        } else {
            rotateY = 0;
        }
        
    }

    /**
     * Accessor for x, y, z, and rotateY, and state
     */
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
    
    public double getRotateY()
    {
        return rotateY;
    }
    
    public String getState()
    {        
        return state;
    }
}
```

Ball class

```java
public class Ball
{

    private double x, y, z, scale;
    private double dir;
    
    /**
     * Constructor for objects of class Ball
     */
    public Ball(double x, double y, double z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 0.2f;
    }

    public void setXYZ(double x, double y, double z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
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
    
    /**
     * Set the direction of the ball
     */
    public void setDir(double angle) 
    {
        dir = angle;
    }
    
    /**
     * Move the ball based on it's direction
     */
    public void move()
    {
        if (dir == -90) {
            x -= 0.1;
        } else if (dir == 90) {
            x += 0.1;
        } else if (dir == 180) {
            z -= 0.1;
        } else if (dir == 0) {
            z += 0.1;
        }
    }   
}
```
