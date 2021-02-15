---
title: "Patterns: Singleton"
date: 2020-09-05
categories: [Development,C++]
tags: [cpp,patterns]
---

## Purpose

Ensures that a class has only one instance and provides a global access point to it.

## Structure

{% plantuml %}
class Singletone {
	+ {static} Singletone* getInstance()
	--
	- {static} Singletone* uniqueInstance
}
{% endplantuml %}

## Participants

* Singleton - singleton:
	* defines a _getInstance_ operation, that allows clients to access a single instance;
	* may be responsible for creating own unique instance.

## Example

```cpp
class MazeFactory {
public:
    static MazeFactory* getInstance();
protected:    
    MazeFactory();
private:
    static MazeFactory* _instance;
};
 
MazeFactory* MazeFactory::_instance = 0;
 
MazeFactory* MazeFactory::getInstance()
{
    if (_instance == 0) {
        _instance = new MazeFactory();
    }
    return _instance;
}
```