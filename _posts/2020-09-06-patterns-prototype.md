---
title: "Patterns: Prototype"
date: 2020-09-06
categories: [Development,C++]
tags: [cpp,patterns]
---

## Purpose

Specifies the types of objects to create using a prototype instance and creates new objects by copying this prototype.

## Structure

{% plantuml %}

class Client {
	+operation()
}

class Prototype {
	+clone()
}
class ConcretePrototype1 {
	+clone()
}
class ConcretePrototype2 {
	+clone()
}

Prototype <|-- ConcretePrototype1
Prototype <|-- ConcretePrototype2
Client --> Prototype

note left of Client
	p = prototype->clone()
end note

{% endplantuml %}

## Participants

* Prototype - prototype:
  * declares an interface for cloning itself.
* ConcretePrototype - concrete prototype:
  * implement operation of cloning itself.
* Client - client:
  * creates new object by asking the prototype to clone itself. 


## Example

```cpp
class MazePrototypeFactory : public MazeFactory {
public:
	MazePrototypeFactory(Maze*, Wall*, Room*, Door*);
	virtual Maze* MakeMaze() const;
	virtual Room* MakeRoom(int) const;
	virtual Wall* MakeWall() const;
	virtual Door* MakeDoor(Room*, Room*) const;
private:
	Maze* _prototypeMaze;
	Room* _prototypeRoom;
	Wall* _prototypeWall;
	Door* _prototypeDoor;
};

MazePrototypeFactory::MazePrototypeFactory(Maze* m, Wall* w, Room* r, Door* d)
{
	_prototypeMaze = m;
	_prototypeWall = w;
	_prototypeRoom = r;
	_prototypeDoor = d;
}

Maze* MazePrototypeFactory::MakeMaze() const
{
	return _prototypeMaze.Clone();
}

Room* MazePrototypeFactory::MakeRoom(int n) const
{
	Room* room = _prototypeRoom.Clone();
	room->Initialize(n);
}

Wall* MazePrototypeFactory::MakeWall() const
{
	return _prototypeWall.Clone();
}

Door* MazePrototypeFactory::MakeDoor(Room* r1, Room* r2) const
{
	Door* door = _prototypeDoor.Clone();
	door->Initialize(r1, r2);
	return door;
}

MazeGame game;
MazePrototypeFactory simpleMazeFactory(
	new Maze, new Wall, new Room, new Door
);
Maze* maze = game.CreateMaze(simpleMazeFactory);

MazePrototypeFactory boombedMazeFactory(
	new Maze, new BoombedWall,
	new RoomWithBoomb, new Door
);
```