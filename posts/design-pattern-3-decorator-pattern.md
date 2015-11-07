<!-- 
.. title: Design Pattern #3: Decorator Pattern
.. slug: design-pattern-3-decorator-pattern
.. date: 2015-11-07 15:48:40 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes for the book Head first design patterns. 

## Overview

* Give objects new responsibilities without making any code changes to the underlying class, even at runtime.

## Example problem

* Starbuzz's `cost()` function for beverage + condiments combinations

* Solution: 

```scala

/**
 * Created by jsun on 11/7/2015 AD.
 */
abstract class Beverage { // abstract component
  def cost:Double
}

class DarkRoast extends Beverage{ // concrete component
  def cost = 2.00
}

// we pass in the decorated object in constructor; decorator object is like a wrapper
// decorator object mirror the types of decorated object (both extends Beverage here)
// a class for condiments only --> not really necessary? 
abstract class CondimentDecorator(beverage: Beverage) extends Beverage // abstract decorator

class Mocha(beverage: Beverage) extends CondimentDecorator(beverage){
  def cost = 0.5 + beverage.cost // "composing" behaviors
}

class Whip(beverage: Beverage) extends CondimentDecorator(beverage){
  def cost = 0.2 + beverage.cost
}

object Main{
  def main(args:Array[String]):Unit = {
    val drink = new Whip(new Mocha(new DarkRoast))
    println(drink.cost) // 2 + 0.5 + 0.2 = 2.7
  }
}


```

## Decorator Pattern

* The Decorator Pattern attached additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

* Decorators have the same supertype as decorated.

* Each decorator HAS-A a component, i.e the decorator has an instance variable that holds a reference to a component

* Decorators are typically created by using other patterns like Factory and Builder.

* Downside: often result in a large number of small classes that can be overwhelming; can increase component instantiation complexity (can be mitigated by Factory/Builder pattern)

* Basically "wrappers"

## Design Principle

* Classes should be open for extension, but closed for modification. Concentrate on areas that are most likely to change in the future.

## Example in real life: `java.io` package

* all decorators: eg. `LineNumberInputStream` -> `BufferedInputStream` -> `FileInputStream`
* abstract component: `InpustStream`
* concrete component: `FileInputStream`, `StringBufferInputStream`, etc
* abstract decorator: `FilterInputStream`
* concrete decorator: `BufferedInputStream`, `LineNumberInputStream`, etc.

## Scala

* Use `implicits` to add behavior without changing original class code (not runtime though)

```scala

object ExtendedModels{
  implicit class ExtendedBeverage(beverage: Beverage){
    def costWithMilk = 0.2 + beverage.cost // Beverage now has a costWithMilk() method, not the best example
  }
}

```
