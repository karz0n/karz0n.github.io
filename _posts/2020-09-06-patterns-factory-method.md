---
title: "Patterns: Factory Method"
date: 2020-09-06
categories: [Development,C++]
tags: [cpp,patterns]
---

## Purpose

Defines the interface for creating an object, but leaves the subclasses to decide which class to instantiate. A factory method allows a class to delegate instantiation to subclasses.

## Structure

{% plantuml %}

class Product
class ConcreteProduct

class Creator {
	+factoryMethod()
}

class ConcreteCreator {
	+factoryMethod()
}

Product <|-- ConcreteProduct
Creator <|-- ConcreteCreator

{% endplantuml %}

## Participants

* Product - product:
	* defines the interface of objects created by the factory method.
* ConcreteProduct - concrete product:
  * implement the interface.
* Creator - creator:
  * declares a factory method, that returns object of type _Product_. _Creator_ can also define a default implementation of a factory method that returns a _ConcreteProduct_ object;
  * can invoke factory method for creating the object of type _Product_.
* ConcreteCreator - concrete creator:
  * override factory method, that returns object of type _ConcreteProduct_.

## Example

```cpp
class MazeGame {
public:
    Maze* CreateMaze();
    
    virtual Maze* MakeMaze() const
    { return new Maze(); }
        
    virtual Room* MazeRoom(int n) const
    { return new Room(n); }
        
    virtual Wall* MakeWall() const
    { return new Wall(); }
        
    virtual Door* MakeDoor(Room* r1, Room* r2) const
    { return new Door(r1, r2); }
};
 
Maze* MazeGame::CreateMaze () {
    Maze* aMaze = MakeMaze();
    
    Room* rl = MakeRoom(l);
    Room* r2 = MakeRoom(2);
    
    Door* theDoor = MakeDoor(rl, r2);
    
    aMaze->AddRoom(rl);
    aMaze->AddRoom(r2);
    
    rl->SetSide(North, MakeWall());
    rl->SetSide(East, theDoor);
    rl->SetSide(South, MakeWall());
    rl->SetSide(West, MakeWall());
    
    r2->SetSide(North, MakeWall());
    r2->SetSide(East, MakeWall());
    r2->SetSide(South, MakeWall());
    r2->SetSide(West, theDoor);
    
    return aMaze;
}
 
class BombedMazeGame : public MazeGame {
public:
    BombedMazeGame();
    
    virtual Wall* MakeWall() const
    { return new BombedWall; }
    
    virtual Room* MakeRoom(int n) const
    { return new RoomWithABomb(n); }
    
};
 
class EnchantedMazeGame : public MazeGame {
public:
    EnchantedMazeGame();
    
    virtual Room* MakeRoom(int n) const
    { return new EnchantedRoom(n, CastSpell()); }
    
    virtual Door* MakeDoor(Room* rl, Room* r2) const
    { return new DoorNeedingSpell(rl, r2); }
    
protected:
    Spell* CastSpell() const;
};
```