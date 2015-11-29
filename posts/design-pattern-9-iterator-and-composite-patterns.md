<!-- 
.. title: Design Pattern #9: Iterator and Composite Patterns
.. slug: design-pattern-9-iterator-and-composite-patterns
.. date: 2015-11-29 16:24:17 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book Head First Design Patterns.

## Starting example: 

Merge two implementation with `ArrayList` and `Array`.

```scala


case class MenuItem (
                 name:String,
                 description:String,
                 vegetarian:Boolean,
                 price:Double){

}

class DinerMenu{
  // dinermenu use array
  var menuItems:Array[MenuItem] = null
  var numberOfItems = 0

  def addItem(item: MenuItem):Unit = {
    menuItems(numberOfItems) = item
    numberOfItems += 1
  }

  def getMenu = menuItems
}

class PancakeMenu{
  // pancakeMenu use arraylist
  var menuItems:util.ArrayList[MenuItem] = null

  def addItem(item: MenuItem):Unit = menuItems.add(item)

  def getMenu = menuItems
}

```

We want to iterate two menus together, without specifying the data type.

Solution: use `Iterator` interface, and define `createIterator` method on both classes

```scala

abstract class Iterator[T]{
  def hasNext:Boolean
  def next:T
}

class DinerMenuIterator(items:Array[MenuItem]) extends Iterator[MenuItem]{
  var i:Int = 0
  def hasNext = i <= items.length
  def next = {
    val menu:MenuItem = items(i)
    i += 1
    menu
  }
}

class PancakeMenuIterator(items:util.ArrayList[MenuItem]) extends Iterator[MenuItem]{
  var i:Int = 0
  def hasNext = i <= items.size()
  def next = {
    val menu:MenuItem = items.get(i)
    i += 1
    menu
  }
}

// add createIterator method:

class DinerMenu{
  // dinermenu use array
  var menuItems:Array[MenuItem] = null
  var numberOfItems = 0

  def addItem(item: MenuItem):Unit = {
    menuItems(numberOfItems) = item
    numberOfItems += 1
  }

  // createIterator replace getMenu
  def createIterator = new DinerMenuIterator(menuItems)
}

class PancakeMenu{
  // pancakeMenu use arraylist
  var menuItems:util.ArrayList[MenuItem] = null

  def addItem(item: MenuItem):Unit = menuItems.add(item)

  def createIterator = new PancakeMenuIterator(menuItems)
}


```

Now we can use the `iterator` interface.

```scala

class Waitress(pancakeMenu: PancakeMenu, dinerMenu: DinerMenu){

  def printMenu = {
    val pancakeIterator = pancakeMenu.createIterator
    val dinerIterator = dinerMenu.createIterator
    printMenu(pancakeIterator)
    printMenu(dinerIterator)
  }

  def printMenu(iter:Iterator) = {
    while(iter.hasNext){
      println(iter.next)
    }
  }

}

```

However, we're still bound to two concrete menu classes --> create a common interface for menu:

```scala

abstract class Menu{
  def createIterator:Iterator
}

class PancakeMenu extends Menu{
...}

class DinerMenu extends Menu{
...}

class Waitress(menus:Seq[Menu]){

  def printMenu = {
    menus.map(_.createIterator).foreach(printMenu(_))
  }

  def printMenu(iter:Iterator) = {
    while(iter.hasNext){
      println(iter.next)
    }
  }
}

```

## Composite pattern

Now what if we want to have a dessert "sub menu"? i.e menus within menus.

```scala

abstract class MenuComponent{
  def isVegetarian:Boolean = false
  def print:Unit
  def getChild(i:Int):MenuComponent = throw new Exception("unsupported")
}

// "Leaf"
class MenuItem(name:String,
                vegetarian:Boolean) extends MenuComponent{
  override def isVegetarian = vegetarian
  def print = println(s"menu item $name")
}

// "Composite"
class Menu(name:String,
            menus:Seq[MenuComponent]) extends MenuComponent{

  // Menu holds MenuComponents --> recursive

  override def getChild(i:Int) = menus(i)
  
  def print = println(s"menu component $name ${menus.foreach(_.print)}") // print all children

}


class Waitress(menuComponent: MenuComponent){
  def print = menuComponent.print
}

```

## Definition

### Iterator: 

* Encapsulate iteration

* Allow access elements of an aggregate (aka `Menu`) sequentially without exposing its underlying representation.

* Also place the task of traversal on the iterator, not on the aggregate

### Composite

* Compose objects into tree structures

* Treat individual objects and compositions uniformly --> Transparency

* Class diagram: `Component` -> `Leaf` or `Composite`
    * `Composite`: holds `Component`s. `addComponent`, `getChild`, `operation`
    * `Leaf`: `operation`

## Design Principle

* A class should be given single responsibility --> have only one reason to change. (eg. aggregate and iterate are two different responsibilities)

## Others

* Use a null iterator can be helpful. (always return false for `hasNext`)
* Can create an external iterator to iterate through Component, eg, find veggie menuItems. 
    * in Scala, can use pattern matching and flatMap

```scala

class Menu(menus:Seq[MenuComponent]) extends MenuComponent{
  ...

  def getVeggie :Seq[MenuItem] = menus.flatMap {
    case m: MenuItem => if (m.isVegetarian) Seq(m) else Nil
    case m: Menu => m.getVeggie
  }

}

```
