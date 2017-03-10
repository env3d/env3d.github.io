---
title: Object Interaction
---
Our game would be pretty boring if we only have one object.  Let's look at how we can create multiple interacting objects in our world.

<iframe width="560" height="315" src="https://www.youtube.com/embed/WDoV2y4xiSw?rel=0" frameborder="0" allowfullscreen></iframe>

# Step 1 - Setup our world

First, create a world with the Scene Creator and put 2 objects in our world.  Let's use Fox and Tux.

 
# Step 2 - Allow user control of Tux

Now, edit the Tux class so we can control the Tux using our keyboard.  Edit the Tux class and add the following move() method (which is automatically called 30 times per second).

```java
    public void move() 
    {
        // Allows simple first person control.  Move by 0.1 unit each time
        // move is called (30 times per second)
        simpleFPSControl(0.1);
    }
```

 The simpleFPSControl() method is a built-in method (inherited from the GameObject class), which allows the user to use WASD and the mouse to control the Tux object.

 

Run the Game class to test your program.

 
# Step 3 - Have the Fox chase the Tux

Here is where we create object interaction.  Our goal is to have the Fox chase the Tux.  This can be accomplished by putting the following move method into Fox.

```java
    public void move()
    {
        // Retrieve an object of Tux class from the environment.  Since we only
        // have one Tux object, this will retrieve our user controlled Tux
        Tux t = getEnv().getObject(Tux.class);

        // a convenient method from GameObject to turn the Fox to face the Tux
        turnToFace(t);
        
        // have the Fox chase our Tux
        moveForward(0.05);
    }
```

 The key observation is the use of getEnv().getObject() method.  Every GameObject has access to the environment by calling the getEnv() method.  Once we have access to the environment, we can call the getObject() method to retrieve whatever object we need.  In the above case, we retrieve an Object of the Tux class. 

 

Run your Game and you'll see that we now have a Fox that chases our user-controlled Tux character.

 
# Exercises

1. The getObject() method returns only one object.  What happens if my world has multiple Tux objects?
1. Modify your program so that it contains multiple Foxes chasing after Tux.

