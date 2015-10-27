<!-- 
.. title: Design Pattern #2: Observer Pattern
.. slug: design-pattern-2-observer-pattern
.. date: 2015-10-26 20:55:32 UTC+08:00
.. tags: Code,Book
.. category: 
.. link: 
.. description: 
.. type: text
-->

## Overview

observer pattern = publisher + subscriber

## Pattern definition:

The observer pattern defines a one-to-many dependency between objects so that when one object changes stats, all of its dependents are notified and updated automatically.

## Starting example: weather monitoring application

```scala

class WeatherStation {
  
  def measurementsChanged():Unit = {
    val temp = getTemperature
    val humidity = getHumidity
    val pressure = getPressure
    
    // we want to make the displays extensible (i.e. decouple them), also maybe subscribe/unsubscribe during runtime
    currentConditionsDisplay.update(temp, humidity, pressure)
    statisticsDisplay.update(temp, humidity, pressure)
    forecastDisplay.update(temp, humidity, pressure)
  }

}

```

## Solution round 1: bad data encapsulation

Note the Double temperature is passed everywhere!

```scala

trait DisplayElement {
  def display:Unit
}

trait Observer{
  def update(temp:Double):Unit
}

trait Subject {

  val observers:ListBuffer[Observer] = ListBuffer[Observer]()

  def registerObserver(observer: Observer) = observers += observer

  def removeObserver(observer: Observer) = observers.remove(observers.indexOf(observer))
  
  def notifyObservers(temp:Double):Unit
}

class WeatherStation extends Subject{

  var temperature:Double = 0.0
  
  def notifyObservers(temp:Double) = observers.foreach(_.update(temp))

  def setMeasurement(temp:Double) = {
    temperature = temp
    notifyObservers(temp)
  }

}


class CurrentConditionDisplay(subject: Subject) extends Observer with DisplayElement{
  // we're passing the subject in constructor so that we can call its register methods and other methods for pull

  subject.registerObserver(this)

  var temperature:Double = 0.0

  def display = println(s"current condition display, temperature: $temperature")

  def update(temp:Double) = {
    temperature = temp
    display
  }

  // NOTE: if using the pull model, would be something like this: 

  def update = {
    temperature = subject.asInstanceOf[WeatherStation].temperature
    display
  }

}

object Main {
  def main(args:Array[String]):Unit = {
    val station = new WeatherStation
    val display = new CurrentConditionDisplay(station)

    station.setMeasurement(10.0)
  }
}


```

## Solution round 2: better data encapsulation

The `java.util.Observer` use `Object` type to pass data around: `update(Observable o, Object arg)`

All the others the same, but we encapsulate data: 

Note: scala's trait proves superior again as you can have concrete implementation but still can compose (as opposed to in Java, it uses a class for `Observable`, thus limiting its reuse potential)

```scala

// DisplayElement and Main stays the same

trait Data

trait Observer{
  def update(subject: Subject, data: Data):Unit // note we pass both subject (so that observers can also use pull model) and data here
}

trait Subject {
  val observers:ListBuffer[Observer] = ListBuffer[Observer]()

  def registerObserver(observer: Observer) = observers += observer

  def removeObserver(observer: Observer) = observers.remove(observers.indexOf(observer))

 def notifyObservers(data: Data):Unit // we pass data to all observers
}

case class WeatherData (temperature:Double) extends Data

class WeatherStation extends Subject{

  var temperature:Double = 0.0

  def notifyObservers(data: Data) = observers.foreach(_.update(this, data)) 

  def setMeasurement(temp:Double) = {
    temperature = temp
    notifyObservers(WeatherData(temp))
  }

}

class CurrentConditionDisplay(subject: Subject) extends Observer with DisplayElement{

  subject.registerObserver(this)

  var temperature:Double = 0.0

  def display = println(s"current condition display, temperature: $temperature")

  def update(subject: Subject, data: Data) = {
    temperature = data.asInstanceOf[WeatherData].temperature // we need to cast to WeatherData
    display
  }

}


```


## Design Principle

Strive for loosely coupled designs between objects that interact. 

## Others

* `push` and `pull` style 
* `java.util.Observer` also has a `setChanged()` method to control notifications.
* Never depend on a specific notification order.


