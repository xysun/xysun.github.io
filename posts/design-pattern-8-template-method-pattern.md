<!-- 
.. title: Design Pattern #8: Template Method Pattern
.. slug: design-pattern-8-template-method-pattern
.. date: 2015-11-26 22:14:29 UTC+08:00
.. tags: Code,Book
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book "Headfirst Design Patterns"

## Example

Make coffee and tea.

```scala

abstract class CaffeineBeverage{
  def prepare: Unit ={
    boilWater
    brew
    pourInCup
    addCondiments
    hook // hook
  }

  def boilWater:Unit = println("boil water")
  def brew:Unit
  def pourInCup:Unit = println("pour in cup")
  def addCondiments:Unit
  def hook:Unit = {} // "hook": for optional part of algorithm
}

class Coffee extends CaffeineBeverage{

  def brew = println("brewing coffee")

  def addCondiments = println("add sugar")
}

class Tea extends CaffeineBeverage{
  
  def brew = println("brewing tea")
  
  def addCondiments = println("add lemon")
}

```

## Definition

* Defines the steps(aka "template") of an algorithm and allow subclass to provide the implementation of one or more steps.
* Keep algorithm's structure unchanged.
* Encapsulate algorithms
* vs Strategy: strategy = encapsulates interchangeable behaviors and use delegation to decide which behavior to use. In strategy, the class that's composed implements the *entire* algorithm. While in the template pattern, the algorithm is incomplete without subclass' implementation.
* Great design for creating frameworks.
* Factory Method pattern is a special case of Template Method Pattern.

## Design principle

* Don't call us, we'll call you.

* Allow low level components to hook themselves into a system, but the high level components determine when they're needed, and how.
