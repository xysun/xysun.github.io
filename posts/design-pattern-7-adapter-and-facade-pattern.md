<!-- 
.. title: Design Pattern #7: Adapter and Facade Pattern
.. slug: design-pattern-7-adapter-and-facade-pattern
.. date: 2015-11-19 22:03:09 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book Head First Design Patterns.

## Adapter pattern

If it walks like a duck and quacks like a duck, it might be a turkey wrapped in a duck adapter.

```scala

bstract class Duck {
  def fly:Unit
  def quack:Unit
}

abstract class Turkey{
  def gobble:Unit
  def fly:Unit
}

class WildTurky extends Turkey{
  def gobble = println("gobble")
  def fly = println("wildturkey fly")
}

class TurkeyAdapter(turkey: Turkey) extends Duck{ // implement the interface of type adapting to,
  // hold a reference to Turkey through constructor: "composed" with adaptee

  def fly = turkey.fly

  def quack = turkey.gobble // translates request from target interface to adaptee interface

}

```

## Facade pattern

Home theater example.

```scala

class HomeTheaterFacade( // access to all subsystems
                       Amplifier amp,
                        DvdPlayer dvd,
                        Projector projector,
                        Screen screen
                         ) {
  
  def watchMovie:Unit = {
    projector.on
    amp.on
    screen.up
    projector.on
    dvd.on
  }
}


```


## Definition

* Adapters: converts the interface of a class into another interface the clients expect. 
* Compare to decorators: decorators add new behaviors.
* Facade pattern: provide a unified interface to a set of interfaces in a subsystem. Subsystem still accessible. Decouple client from subsystem.

## OO principle

* The principle of least knowledge -- talk only to your immediate friends.

When design a system, be careful of the number of classes it interacts with. -- don't have a large number of classes coupled together.

We should only invoke methods that belong to

* the object itself
* objects passed in as parameter of the method
* any object the method creates 
* any component of the object
* **DO NOT** call method of an object returned by another call

Example: 

without the principle:

```java

public float getTemp(){
    Thermometer thermometer = station.getThermometer(); // DON'T: we're using an object returned by another class, we depend on both station and thermometer class
    return thermometer.getTemperature();
}

```

with the principle:

```java

public float getTemp(){
    return station.getTemperature(); // station is a component of the object, ok to call its method
}

```

