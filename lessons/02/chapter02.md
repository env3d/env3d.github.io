---
title: Creating A Single Class
---
Before working on this lesson, you must understand the following concepts:

* Objects are created from classes.
* Programmers create class definitions.
* The basic syntax of if/else structure.

# Creating a single class

Download the template project, unzip it and open it with BlueJ.

Create a new Java class with 4 instance variables of the double type: x, y, z, and scale:

```java 
public class Ball
{
    private double x;
    private double y;
    private double z;
    private double scale;

    public Ball()
    {
        x = 5;
        y = 1;
        z = 5;
        scale = 1;
    }
} 
```

After this class is compiled, create an object of this class and select "start env" from the tools menu. A new window will pop up with your first 3D object displayed at the center. The default shape of the object is a ball, the the default texture is a picture of the earth.

The Ball class is not very exciting right now. Let's add a method to change its size.

The size of the 3D object is determine by the "scale" field. The larger the scale, the large the object becomes. Add the following method into your Ball class:

```java
    public void feed()
    {
        scale = scale + 0.1;
    }
```

Every time this "feed()" method is called, the Ball's size will be increased by 0.1. Start the Env3D environment and call this method a few times to see the ball grow in size right in front of your eyes.

# Valid Env3D fields

There are a total of 10 fields that are valid for Env3D objects:

* x, y, z, scale, rotateX, rotateY, rotateZ - these fields must have the "double" type.
* model, texture - these fields have the "String" type.
* transparent - this field has a "boolean" type. If the texture has a transparency channel (a.k.a. alpha channel), setting this field to true would make the texture transparent.

If you look at the Doty code from lesson 1, you have already seen how the texture field is used to map a picture onto the sphere. The model field works in a similar way but changes the sphere into a different shape. Shapes are stored in "obj" files and I have packaged several of these files under the "models" directory within the project folder.

# Exercises

1. Using the "if/else" structure, modify the "feed()" method so that the scale cannot go beyond 2.
2. Create a new field called "texture". This field has the "String" type. Make the texture point to one of the picture files under the "textures" directory within the project folder.
3. Perform the following:
   1. Set the default texture for the Ball class to "textures/doty.gif".
   2. Create a method with the signature "public void pet()".
   3. Make it so that if the scale is less than 1.5, a call to the pet method would make the Ball angry. Otherwise, a call to the pet method would make the Ball happy.
4. Create an "exercise" method that would decrease the scale by 0.1.
5. [Challenge] Limit the total number of method calls to 10. If the caller make more than 10 method calls, change the ball's texture to "textures/doty_sad.gif". At this point, all method calls will have no effect.
6. [Project] Design your own class with at least 3 methods, and make changes to at least 2 states. Make it a game so that there is a winning condition and a losing condition.



