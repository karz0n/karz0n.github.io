---
title: "Patterns: Builder"
date: 2020-09-05
categories: [Development,C++]
tags: [cpp,patterns]
---

## Purpose

Separates the construction of a complex object from its views, so that different representations can result by the same construction process (sequence).

## Structure

{% plantuml %}

class Director {
	+ construct()
}

class Builder {
	+ buildPart()
}

class ConcreteBuilder {
	+ buildPart()
	+ getResult()
}

Builder <|-- ConcreteBuilder

note left of Director
	void construct() {
        For all objects in structure {
           builder->buildPart()
        }	
	}
end note
{% endplantuml %}

{% plantuml %}

activate aClient
aClient -> aClient : new ConcreteBuilder
aClient -> aClient : new Director{aConcreteBuilder}
aClient -> aDirector : construct()
activate aDirector
aDirector -> aConcreteBuilder : buildPartA()
aDirector -> aConcreteBuilder : buildPartB()
aDirector -> aConcreteBuilder : buildPartC()
deactivate aDirector
aClient -> aConcreteBuilder : getResult()
deactivate aClient

{% endplantuml %}


## Participants

* Builder - builder:
	* set abstraction interface for creating object _Product_ by parts.
* ConcreateBuilder - concrete builder.
	* designs and assembles product parts by implementing _Builder_ abstract interface;
	* defines the created view of product and monitors it;
	* provides interface for access to product.
* Director - disposer:
	* constructs object using _Bulder_ interface.
* Product - concrete product:
	* represents a complex construct. _ConcreateBuilder_ build internal representation of the product and defines the process of its assembly;
	* includes classes that define constituent parts, including interfaces for assembling the final result from parts.

## Example

```cpp
class MazeBuilder {
public:
    virtual void BuildMaze() { }
    virtual void BuildRoom(int room) { }
    virtual void BuildDoor(int roomFrom,  int roomTo) { }
 
    virtual Maze* GetMaze() { return 0; }
protected:
    MazeBuilder();
}
 
class StandardMazeBuilder : public MazeBuilder {
public:
    StandardMazeBuilder();
 
    virtual void BuildMaze();
    virtual void BuildRoom(int);
    virtual void BuildDoor(int, int);
 
    virtual Maze* GetMaze();
private:
    Direction CommonWall(Room*, Room*);
    Maze* _currentMaze;
}
 
StandardMazeBuilder::StandardMazeBuilder() {
    _currentMaze = 0;
}
 
void StandardMazeBuilder::BuildMaze () {
    _currentMaze = new Maze;
}
 
Maze* StandardMazeBuilder::GetMaze() {
    return _currentMaze;
}
 
void StandardMazeBuilder::BuildRoom(int n) {
    if (!_currentMaze->RoomNo(n)) {
        Room* room = new Room(n);
        _currentMaze->AddRoom(room);
 
        room->SetSide(North, new Wall);
        room->SetSide(South, new Wall);
        room->SetSide(East, new Wall);
        room->SetSide(West, new Wall);
    }
}
 
void StandardMazeBuilder::BuildDoor(int n1, int n2) {
    Room* r1 = _currentMaze->RoomNo(n1);
    Room* r2 = _currentMaze->RoomNo(n2);
    Door* d = new Door(r1, r2);
 
    r1->SetSide(CommonWall(r1, r2), d);
    r2->SetSide(CommonWall(r2, r1), d);
}
 
Maze* MazeGame::CreateMaze(MazeBuilder& builder) {
    builder.BuildMaze();
 
    builder.BuildRoom(1);
    builder.BuildRoom(2);
    builder.BuildRoom(1, 2);
 
    return builder.GetMaze();
}
```