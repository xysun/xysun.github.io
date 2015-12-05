<!-- 
.. title: Design Pattern #10: State Pattern
.. slug: design-pattern-10-state-pattern
.. date: 2015-12-05 22:14:02 UTC+08:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes for the book Headfirst Design Patterns.

## Example

Gumball machine.

### Implementation 101: focus on action

* Define states: No quarter; Has quarter; Out of gumballs; Gumballs sold

* Use instance variable to hold state

```scala

  final val SOLD_OUT:Int = 0
  final val NO_QUARTER:Int = 1
  final val HAS_QUARTER:Int = 2
  final val SOLD:Int = 3
  
  var state:Int = SOLD_OUT

```

* Define actions: insert quarter; eject quarter, turn crack, dispense

* For each action, create a method using conditional statements to determine which behavior is appropriate in each state.

```scala

  def insertQuarter = state match {
      // go through every state
      case HAS_QUARTER | SOLD_OUT | SOLD => println("can't insert quarter now")
      case NO_QUARTER => {state = HAS_QUARTER;} // change state
  }

```

* Not very flexible for adding new states
* State transitions are not explicit --> buried in `if` statements.

### Implementation 201: focus on state

* Localize behavior for each state: each state implements its own actions.

* Define a `State` interface that contains a method for every action.

```scala

abstract class State{
  def insertQuarter:Unit
  def ejectQuarter:Unit
  def turnCrank:Unit
  def dispense:Unit
}

```

* Implement a `State` class for every state.

```scala

class NoQuarterState(machine: GumballMachine) extends State{
  // compose with the machine

  def insertQuarter = machine.setState(machine.hasQuarterState) // change machine's state

  def ejectQuarter = println("cannot eject quarter")

  def turnCrank = println("no quarter")

  def dispense = println("no quarter")
}

```

* Delegate to the state class in parent object.

```scala

class GumballMachine {

  val hasQuarterState = new HasQuarterState(this)

  var state:State = new NoQuarterState(this) // start with no quarter

  def setState(_state: State):Unit = state = _state

  def insertQuarter = state.insertQuarter // delegate

}

```

## Definition

* Allows an object to alter its behavior when its internal state changes.
* Similar to strategy pattern: 
    * strategy pattern: client specifies the strategy that is composed
    * state pattern: client usually knows very little about internal state
