---
title: Models and Animation Exercise
---
# Introduction

Env3D has the ability to load a 3D model file into the environment (so you don't have to use spheres all the time). These 3D models are usually created using a 3D modeling program such as Blender or 3D Studio Max.

Env3D can import any 3D models that are saved in the OBJ format. If you are using the stable version, download the models.zip file and unzip it into the models directory of the template project.  If you are using the development version, many animated models are already included in the template.  Simply select "Tools->Env3D->Extract additional models" to extract and install these models into the models directory.
 

Let's start by loading a model. Create a class called Monster as follows:

```java
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
        
    /**
     * Constructor for objects of class Monster
     */
    public Monster(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 1;
        rotateY = 0;
        texture = "models/Boss/boss.jpg";
        model = "models/Boss/Idle/bossidle00.obj";
    }
}
```

Create an object of the Monster class (place it at location 5, 1, 5) and use the "Start env3d" menu item under the "Tools" menu. You should see a little monster in the middle of the room.

The only thing that we did was to add a new field called "model" into our Class. This will load the corresponding OBJ file into our environment.
Creating animation

If you look at the models directory, you'll notice that under the Boss/Idle/ directory, there are 20 model files. If we load each model in order of the file name very quickly, it will appear that the monster is moving.

Let's modify our Monster class to make it animate. First, we create an ArrayList of Strings to hold all the filenames to the OBJ files, like so:

```java
import java.util.ArrayList;

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

    // An arraylist to hold all the models
    private ArrayList<String> idleModels;
        
    /**
     * Constructor for objects of class Monster
     */
    public Monster(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 1;
        rotateY = 0;
        texture = "models/Boss/boss.jpg";

        // Create the ArrayList and populate it with the various model files
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

        // The default model
       model = idleModels.get(0);
    }
}
```

Next, we need to create a move() method to mutate the model field. Each time the move() method is called, we want to load the next model from the idleModels ArrayList.

To accomplish this, we need a way to remember what is the current model number that we are currently using. The easiest way to implement this is to add a new field into our Monster class: "private int frame"

Every time the move() method is called, we will increment the frame field by 1. We will then retrieve the corresponding model number from the ArrayList and put it into the model field. The completed Monster class looks like the following:

```java
import java.util.ArrayList;

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

    // An arraylist to hold all the models
    private ArrayList<String> idleModels;
    private int frame;
        
    /**
     * Constructor for objects of class Monster
     */
    public Monster(double x, double y, double z)
    {
        // initialise instance variables
        this.x = x;
        this.y = y;
        this.z = z;
        scale = 1;
        rotateY = 0;
        texture = "models/Boss/boss.jpg";

        // Create the ArrayList and populate it with the various model files
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

        // The default model
        frame = 0;
        model = idleModels.get(frame);
    }

    public void move()
    {
        // Increment the frame counter and cycle back to 0 if the limit is reached.
        frame = frame + 1;
        if (frame >= idleModels.size()) {
            frame = 0;
        }
        // load the next model in sequence
        model = idleModels.get(frame);
    }
}
```

Once again, use the "Start env3d" menu item to inspect the object in 3D.

# Exercise

1. Create a Game class and add a Monster object into the environment. Call the Monster.move() method inside the game loop. You will see the idle animation plays.
2. [Challenge] Add the "roar" animation by first creating an ArrayList to hold all the roar models. Make it so that if the "R" key is pressed, the roar animation would play.

