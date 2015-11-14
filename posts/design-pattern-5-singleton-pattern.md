<!-- 
.. title: Design Pattern #5: Singleton Pattern
.. slug: design-pattern-5-singleton-pattern
.. date: 2015-11-14 15:48:26 UTC+08:00
.. tags: Book, Code
.. category: 
.. link: 
.. description: 
.. type: text
-->

These are my notes about the book Head First Design Patterns.

## Definition: 

* Ensure one and only one object is instantiated for a given class, and provides a global point of access. 

* Example usage: global resources like connection/thread pools

* For resource intensive objects, can implemented in lazy manner.

## Java implementation: 

Use a private constructor.

```java

/**
 * Created by jsun on 11/14/2015 AD.
 */
public class Singleton {

    private static Singleton uniqueInstance = null;

    private Singleton(){};
    
    // NOT THREAD SAFE!
    public static Singleton getInstance() { // others have to call this method to get instance
        if (uniqueInstance == null){
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
    
    // other methods below

}


```

## To be thread safe

* Use `synchronized`. Expensive (can decrease performance by 100x) and we actually only need to synchronize during the first time.

```java

public static synchronized Singleton getInstance()

```

* Create eagerly

JVM will ensure instance created before any thread access.

```java

public class Singleton {

    private static Singleton uniqueInstance = new Singleton();

    private Singleton(){};

    public static Singleton getInstance() {
        return uniqueInstance;
    }
    
}


```

* Double checked locking: only synchronize if instance is not created yet.

```java

public class Singleton {

    private volatile static Singleton uniqueInstance = new Singleton();

    private Singleton(){};

    public static Singleton getInstance() {
        
        if (uniqueInstance == null){
            synchronized (Singleton.class){
                if (uniqueInstance == null){ // check again
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
    
}


```

## Scala: 

* Scala has native support for singleton: `Object`.
