<!-- 
.. title: Design Pattern #4: Factory Pattern
.. slug: design-pattern-4-factory-pattern
.. date: 2015-11-10 21:43:45 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

## Example problem: pizza shop

```scala

  // this code will need to change if pizza types change in the future
  // i.e not "closed for modification"

  def orderPizza(pizzaType:String):Pizza = { // Pizza is a trait or abstract class
    val pizza = pizzaType match {
      case "cheese" => new CheesePizza
      case "greek" => new GreekPizza
      case "pepperoni" => new PepperoniPizza
    }
  
    pizza.prepare()
    pizza.bake()
    pizza.cut()
    pizza.box()
    
    pizza

```

## "Simple Factory Pattern"

* Use an object to handle object creation.
* One implementation with no subclassing.

```scala

class SimplePizzaFactory{

  def createPizza(pizzaType:String):Pizza =
    pizzaType match {
      case "cheese" => new CheesePizza
      case "greek" => new GreekPizza
      case "pepperoni" => new PepperoniPizza
    }
}

// we pass factory in constructor
class PizzaShop(factory: SimplePizzaFactory) {

  def orderPizza(pizzaType:String):Pizza = {
    
    val pizza = factory.createPizza(pizzaType)

    pizza.prepare()
    pizza.bake()
    pizza.cut()
    pizza.box()

    pizza

  }
}


```

The Good:

* Object creation is now encapsulated. 
* Multiple instances can share one same factory. 

What if we need multiple factories? 

```scala

val nyPizzaFactory = new NYPizzaFactory // extends SimplePizzaFactory
val nyPizzaStore = new PizzaShop(nyPizzaFactory)

val chicagoPizzaFactory = new ChicagoPizzaFactory
val chicagoPizzaStore = new PizzaShop(chicagoPizzaStore)

```

hmm...

## Factory Method Pattern

```scala

/**
 * Created by jsun on 11/10/2015 AD.
 */

abstract class Pizza{
  def prepare:Unit
  def bake:Unit
  def cut:Unit
  def box:Unit
}

// we pass factory in constructor
abstract class PizzaShop {
  
  def createPizza(pizzaType:String):Pizza // subclass must implement this "factory method"

  def orderPizza(pizzaType:String):Pizza = {
    
    val pizza = createPizza(pizzaType)

    // the rest procedures are localized and will be consistent

    pizza.prepare
    pizza.bake
    pizza.cut
    pizza.box

    pizza

  }
}


class NYPizzaShop extends PizzaShop{
  def createPizza(pizzaType:String) = new NYPizza
}


```

* Defer object creation to subclass
* Can localize other procedures (eg. `prepare`, `bake`, `cut`, `box`)
* `Creator` class (PizzaShop) + `Product` class (Pizza). "Parallel classes"
* However, creating *one* product only.

Now what if you have different families of pizza ingredients? 

## Abstract Factory Pattern

```scala

// "abstract factory"
abstract class PizzaIngredientFactory{
  def createDough:Dough // "Factory Methods"
  def createSauce:Sauce
  // will need to change interface if new ingredients are being added
}

// concrete factory
class NYIngredientFactory extends PizzaIngredientFactory{
  def createDough = new ThinCrustDough
  def createSauce = new MarinaraSauce
}


abstract class Pizza{
  var dough:Dough
  var sauce:Sauce
  
  def prepare:Unit
  def bake:Unit
  def cut:Unit
  def box:Unit
}

// use concrete factories through composition
class CheesePizza(ingredientFactory: PizzaIngredientFactory) extends Pizza{
  
  dough = ingredientFactory.createDough
  sauce = ingredientFactory.createSauce
  
}

class NYPizzaShop extends PizzaShop{
  
  def createPizza(pizzaType:String) = {
    val ingredientFactory = new NYIngredientFactory
    pizzaType match {
      case "cheese" => new CheesePizza(ingredientFactory)
    }
  }
}

```

* For multiple families of object
* Definition: create families of related or dependent objects (pizza ingredients here)  without specifying their concrete classes.

## Design Principle (Dependency Inversion Principle)

* Depend upon abstractions. Do not depend upon concrete classes. 

* No variables should hold a reference to a concrete class. (eg. using `new`)

* No class should derive from a concrete class.

* No method should override an implemented method of any of its base classes.



## Reference

* [https://web.cs.dal.ca/~jin/3132/lectures/dp-07.pdf](https://web.cs.dal.ca/~jin/3132/lectures/dp-07.pdf)
* [http://stackoverflow.com/questions/13029261/design-patterns-factory-vs-factory-method-vs-abstract-factory](http://stackoverflow.com/questions/13029261/design-patterns-factory-vs-factory-method-vs-abstract-factory)
* [http://corey.quickshiftconsulting.com/blog/first-post](http://corey.quickshiftconsulting.com/blog/first-post)
