<!-- 
.. title: Design Pattern #6: Command Pattern
.. slug: design-pattern-6-command-pattern
.. date: 2015-11-15 20:08:34 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book Head First Design Patterns.

## Example problem

Home automation remote control: on, off, undo. Supporting extensible commands.

## Cook example

* Customer <--> `Client`: customer create command object and call `setCommand` on invoker
* Order <--> `Command`
* Waitress <--> `Invoker`: can call `execute` on the command objects.
* Cook <--> `Receiver`


```scala

class Order(cook:Cook) { // hold reference to the actual "actor"
  
  def orderUp() : Unit = { // interface that encapsulates the action
    cook.makeBurger // can vary among different command objects
  }

}

```

## Simple remote control with one slot

```scala

abstract class Command {
  def execute:Unit
}

class LightOnCommand(light:Light) extends Command{ // command object
  def execute = light.on
}

class RemoteControl{ // invoker
  var slot:Command = null

  def setCommand(cmd:Command):Unit = slot = cmd

  def buttonPressed:Unit = slot.execute
}

object Main{ // client
  def main(args:Array[String]):Unit = {
    val control = new RemoteControl
    control.setCommand(new LightOnCommand(new Light))
    control.buttonPressed
  }
}

```

## Support undo: 

Keep track of last command called.

```scala

// NOTE: these are NOT gramatically correct

abstract class Command {
  def execute:Unit
  def undo:Unit
}

class LightOnCommand(light:Light) extends Command{ // command object
  def execute = light.on
  def undo = light.off
}

class RemoteControl{ // invoker
  val slots:Seq[Command] = Nil

  var undoCommand:Command = NoCommand
  
  def setCommand(i:Int, cmd:Command):Unit = slots[i] = cmd

  def buttonPressed(i:Int):Unit = {
    slots(i).execute
    undoCommand = slots(i) // keep track of last command 
  }
  
  def undo = undoCommand.undo
}

```

To support a history of undos, keep a stack. 

## Use a macro command

```scala

class MacroCommand(commands:Seq[Command]) extends Command {
  def execute = commands.foreach(_.execute)
}

```


## Definition

* Encapsulating a request as an object. Decouple the requester of an action from the object that actually performs the action.
* "Store them, pass them around, and invoke them when you need them"
* Roles:
    * Client: create a concrete command and set its receiver
    * Invoker: holds a command and call its `execute` method
    * Receiver: know how to carry out the request
    * Command: provide `execute` (and `undo`) method. Bind together a set of actions on a specific receiver.

## Others

* Implementing a `NoCommand` can be useful (no need to check `null` any more). aka "null object" (better handled by `Option` type in scala)
* More usages:
    * Job queues: have a group of threads sit on end of the queue, retrieve a command object, call its `execute` method.
    * Logging requests: as we execute commands, we store a history of them on disk (command object supports `save` method). When a crash occurs, we reload the command objects (`reload` method of command object) and invoke `execute` in order. --> can support all-or-nothing transaction.

