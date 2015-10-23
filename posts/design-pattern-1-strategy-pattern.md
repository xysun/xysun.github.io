<!-- 
.. title: Design pattern #1: Strategy Pattern
.. slug: design-pattern-1-strategy-pattern
.. date: 2015-10-23 18:30:45 UTC+08:00
.. tags: Code, Book
.. category: 
.. link: 
.. description: 
.. type: text
-->

This is my notes about the book [Head first design patterns](http://shop.oreilly.com/product/9780596007126.do).

## Overview 

Strategy pattern: encapsulating behaviors / algorithms.

## Starting example

We start with a duck base class and several concrete duck classes: 

```scala

abstract class Duck {

  def quack:Unit = println("quack")

  def display:Unit

}

class RedDuck extends Duck{
  def display:Unit = println("RedDuck")
}

class RubberDuck extends Duck {
  def display:Unit = println("rubber duck")
}

```

Now we want to add fly behavior, but ***only to some of the ducks***. 

Inheritance is not the right solution, because it'll add `fly` to all subclasses. 

## General problem: 

Duck behavior keeps changing across subclasses, and it's not appropriate for *all* subclasses to have those behaviors

## Book's solution:

Encapsulate ***behavior*** in classes. And assign behavior to the instances of Duck, and able to change dynamically.

```scala

trait FlyBehavior{
  def fly:Unit
}

class FlyWithWings extends FlyBehavior{
  def fly = println("fly with wings")
}

class FlyNoWay extends FlyBehavior{
  def fly = () // do nothing
}


abstract class Duck
{

  val flyBehavior:FlyBehavior // use super type here,

  // NOTE: we can also use var for flyBehavior and a setter method to change behavior in runtime

  def performFly:Unit = flyBehavior.fly // delegate to behavior class

  def display:Unit

}


class RedDuck extends Duck{

  val flyBehavior = new FlyWithWings

  def display:Unit = println("RedDuck")
}

class RubberDuck extends Duck {

  val flyBehavior = new FlyNoWay // rubber duck cannot fly

  def display:Unit = println("rubber duck")
}

object Main{
  def main(args:Array[String]): Unit ={
    val redDuck = new RedDuck
    redDuck.performFly
  }
}


```


## Design principle:

* Identify the aspects of your application that vary and separate them from what stays the same. (In this case, behavior is what varies)
* Program to an interface/supertype, not an implementation. (Use interface for behaviors)
* Favor composition over inheritance: "HAS-A" rather than "IS-A"

## Pattern definition: 

Strategy pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it. 

In our example, behavior is our "algorithm"

## Other notes:

* The importance of having a shared vocabulary
* OO basics: abstraction; encapsulation; polymorphism; inheritance

## How about using traits?

Unlike interface in Java, traits in scala can have concrete implementations, so we have code reuse. 

Compared to book's solution, however, I'm unable to update the behavior in runtime. 

```scala

abstract class Duck {

  def quack:Unit = println("quack")

  def display:Unit

}

trait FlyableDuck{
  def fly = println("flying!")
}

class RedDuck extends Duck with FlyableDuck{
  def display:Unit = println("RedDuck")
}

class RubberDuck extends Duck {
  def display:Unit = println("rubber duck")
}

```

