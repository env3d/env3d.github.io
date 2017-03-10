---
title: Creating Objects
---

Before starting this lesson, you should know the following concepts:

* The basic concept in Object Oriented programming is the notion of a class.
* You create objects from a class. Objects would then perform computation.
* The relationship between Objects and Classes is similar to the relationship between a cookie cutter and actual cookies.
* In a computer program may contain anywhere from one to thousands of objects.
* Objects have methods. Methods allow an object to perform certain functionality.
* Some methods have parameters. Parameters allow the caller of the object to provide additional information to the methods.
* Objects have fields. They represent the object's state.
* You can create many objects from the same class. All objects will have the same number of fields, but the values of the fields inside each object can be different.

# Creating 3D Objects

Download and unzip the template project into a directory of your choice, and open this project in BlueJ. Click on "New Class". Name the class "Doty", and replace the content of the class with the following (don't worry if you don't understand the content of this class, we are only interested in using the class at this point):

```java
/**
 * A simple class to demonstrate Env3D 
 */
public class Doty
{
    private double x;
    private double y;
    private double z;
    private double rotateX;
    private double rotateY;
    private double rotateZ;
    private double scale;
    private String texture;
     
    /**
     * Constructor 
     */
    public Doty()
    {
        // initialise instance variables
        x = 5;
        y = 2;
        z = 5;
        rotateX = 0;
        rotateY = 0;
        rotateZ = 0;
        scale = 1;
        texture = "textures/doty.gif";
    }
     
    public void setXYZ(double x, double y, double z)
    {
        this.z = z;
        this.y = y;
        this.x = x;
    }    
     
    public void talk() 
    {
        texture = "textures/doty_talk.gif";
    }
         
    public void happy() 
    {
        texture = "textures/doty_happy.gif";
    }
    public void angry() 
    {
        texture = "textures/doty_angry.gif";
    }    
 
    public void surprise() 
    {
        texture = "textures/doty_surprise.gif";
    }
     
    public void sad() 
    {
        texture = "textures/doty_sad.gif";
    }
      
    public void setRotateX(double ang) {
        rotateX = ang;
    }
 
    public void setRotateY(double ang) {
        rotateY = ang;
    }
 
    public void setRotateZ(double ang) {
        rotateZ = ang;
    }
     
    public String toString() {
        return "Doty position: "+x+" "+y+" "+z;   
    }
     
    public double getVolume() {
        double radius = scale/2;
        return (4/3f*3.1416f*radius*radius*radius);
    }
}
```

Here we have a Java computer program with 1 class. The name of this class is Doty. Click on the "Compile" button. As you can see, if you right click on the Doty class, the only useful thing that you can do with it is to create a new Doty. Let's create a Doty object. We'll give the name "doty1" (notice that it is a convention that class names start with uppercase and object names start with lowercase). The bottom window shows all the objects that have been created. You can take a look at the doty1 object in env3d by starting the "env3d" extension from the "Tools" menu. If you right click on the doty1 object, you'll see that you are presented with multiple actions that you can do with it. In Object Oriented terms, these actions are called "methods".

# Exercises

1. Create 1 object of the Doty class. Change its state to make it smile.
1. Use the "inspect" option from the object menu, make a list of all the fields that the Doty class has, and provide the data type and an explanation for each field.
1. Create 2 objects of the Doty class, and turn them so that they face each other.
1. (Optional) Create new expressions for Doty.
