---
title: Polymorphism
---
Before you begin, make sure that you understand

* How a class can inherit from another class, creating a parent-child relationship
* A variable of the parent class can be used to hold objects of its subclass

# Behaving differently

The structure of our "predator and prey" simulation now looks pretty good. We can easily add new creatures without having to write lots of duplicated code, and our game class can process all creatures using just one list, making adding of new creatures essentially a one-line affair. However, if you look at our Fox and Tux code, you'll notice that they only contain the constructor, and the only difference between those 2 classes are the texture and model. Surely there must be more that we can do with inheritance?

To answer that question, we need to look at our move() method. Recall that if you call a method for a particular object and the method does not exist in that class, java would look at its parent class for the method. If the method appears in the parent class, java would execute the method in the parent class.

In our program, we defined a move() method in the Creature class. The Creature's move() method is called once per frame to move each creature. Both our Fox and Tux share this same method and moves in the same way. What if we want the Fox to move differently?

We can achieve this by simply creating an additional move() method inside the Fox class. For example, let look at the following modified Fox class:

```java
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
    public void move() 
    {
        // We have to use the accessor and mutators, as we cannot access the
        // private fields in the parent class.
        setX(getX()+getScale()*dir);
        
        // Change the direction when we are out of reach
        if (getX() < getScale() || getX() > 50-getScale()) {
            dir = dir * -1;
        }
    }
}
```

The move() method defined here simply moves the Fox left and right in our environment. That's all it takes to change the Fox's moving behaviour! When the game loop calls the move() method for each creature, if the creature is a Fox, java will automatically called the Fox's move() method.

In computer science, the idea that calling the same method resulting different code to be executed is called polymorphism. In our case, our game processing loop is simply calling the creature's move() method. However, since a creature can be either Tux or Fox, when the underlying object is a Fox, the Fox's move() method is called instead of the Creature's move() method.

Polymorphism is an extremely powerful concept and results in code that is very maintainable and extensible if used correctly.

# Exercises

* Modify your Fox class as shown above. You'll also need to change something else to make your program run. What is it that you have to change?
* Change the behavior of Tux so that moves differently than Fox.
* Implement the following rules into your simulation:
  * Make the fox die (disappear) if it has not eaten anything after 50 turns.
  * When a tux is eaten by a fox, 10% of the time, create a new fox in place of the dead tux.
  * When a tux is close to another tux, 10% of the time, a new tux is born.
  * When 5 or more tuxes are close in proximity, they all get overcrowded and die.

