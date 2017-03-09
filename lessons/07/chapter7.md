---
title: More Inheritance
---
Before you begin, you must understand:

* Basic inheritance

# Further Re-factoring

With basic inheritance, we have simplified our Fox and Tux classes considerably by creating a Creature class to hold all the common parts of two classes. A change in the Creature class will affect both of the sub-classes. However, when you tried to add a new creature into our game, you'll noticed 2 things:

* It is relatively easy to create a new creature by inheriting from the Creature class
* Adding a new creature to the game involves modifying many parts of the Game class, including:
    * Adding a new field to keep track of the new creature
    * Modifying the play method to add the new creature to the environment
    * Modifying the game loop to add collision logic to the game

That's a lot of work to do to add a new creature! If we want our game to be maintainable, we must find a way to add new creatures easily. Ideally, all it takes is a one line to construct and add the new creature to our game.

What prevents us from accomplishing our goal is the fact that our game only deals with specific creatures, for example, in our game loop:

```java
            // Move each fox and tux.
            for (Fox f : foxes) {
                f.move();
            }
            
            for (Tux t : tuxes) {
                t.move();
            }
```

Noticed that since we have separate lists for foxes and tuxes, we need to deal with them separately. Adding a new creature means a new list and a separate code block to process that list. What if we can have one list to hold all of our creatures? It turns out we can! Since both Tux and Fox shares the same parent class, we can create a list of Creature to hold both, like so:

```java
    private ArrayList<Creature> creatures;

    /**
     * Constructor for the Game class. It sets up the foxes and tuxes.
     */
    public Game()
    {
        // we use a separate ArrayList to keep track of each animal. 
        // our room is 50 x 50.
        creatures = new ArrayList<Creature>();
        for (int i = 0; i < 55; i++) {
            if (i < 5) {
                creatures.add(new Fox((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));        
            } else {
                creatures.add(new Tux((int)(Math.random()*48)+1, 1, (int)(Math.random()*48)+1));
            }
        }
    }
```

We have modified our constructor for the Game class to contain a list of creatures, instead of separate lists for foxes and tuxes. Notice that we can freely put either Fox or Tux objects into the creatures list, since objects of both Fox and Tux can be qualified as Creature objects.

In our main game loop, we are now only required to process the creatures list once to move all of the objects, like so:

```java
            for (Creatures c : creatures) {
                c.move();
            }
```

But wait! What is going to happen to our collision detection code? Since we no longer have 2 lists, how can we compare each fox to each tux? There are many ways to do this, but I will use the following code:

```java
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
```

In the above code, we simply compare each object to all the other objects. The instanceof operator is used to determined if the Creature object is a Fox or a Tux.

# Exercise

* Create your new Game class based on the above lesson
* Add your Hunter object to your Game.
* Come up with a different way to process your Fox, Tux, and Hunter collision.

