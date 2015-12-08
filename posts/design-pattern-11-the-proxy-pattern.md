<!-- 
.. title: Design Pattern #11: The Proxy Pattern
.. slug: design-pattern-11-the-proxy-pattern
.. date: 2015-12-06 21:15:17 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes for the book Headfirst Design Patterns.

## Example

Monitor gumball machine remotely.

* Java's RMI: 
    * client object --> client helper (proxy, "stub") --> service helper ("skeleton") --> service object
    * RMI: build client helper + service helper
    * steps:
        * make a remote interface: defines available remote methods
        * make a remote implementation (aka remote service)
        * create stub and skeleton using rmic (both proxy and real object use same interface)
        * start rmi registry
        * start remote service
    * client: does a lookup on RMI registry, RMI registry deserialize and return the stub object. (client must have the stub class, although it always uses the remote interface)

## Definition

* Provides a surrogate or placeholder for another object to control access to it. Maybe remote, expensive to create (virtual proxy) or in need of securing.
* Control and manage access
* Remote proxy: acts as a local representative to a remote object.
* Virtual proxy: delegate requests directly to object after it is created.
* Protection proxy: `invocationHandler` decides what method to call on actual object.


