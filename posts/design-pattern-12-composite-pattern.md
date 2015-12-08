<!-- 
.. title: Design Pattern #12: Composite Pattern
.. slug: design-pattern-12-composite-pattern
.. date: 2015-12-07 21:25:30 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book Headfirst Design Patterns. 

The last chapter, finally!

## MVC

* Controller: manipulates model; can ask View to change as well (holds reference to both Model and View)
* Model: keep state & data; tell View the state has changed (does not know View or Controller -- they register as observers)
* View: update display; tells Controller of user input; request Model for data (holds reference to both Model and Controller)

## Design patterns in MVC

* Model: Observer pattern to keep View and Controller updated; sometimes use Adapter pattern to adapt a model to work with existing views and controllers.
* Controller: Strategy pattern. Controller is the "Strategy" behavior for the View. Swappable.
* View: Composite pattern to manage the windows/display.


## Definition

* A set of patterns combined to solve a general problem.

