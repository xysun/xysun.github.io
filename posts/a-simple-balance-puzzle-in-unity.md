<!-- 
.. link: 
.. description: 
.. tags: draft, code
.. date: 2014/02/23 22:43:56
.. title: A simple balance puzzle in Unity
.. slug: a-simple-balance-puzzle-in-unity
-->

In this post I describe how to create a simple balance puzzle in Unity 2D.

* Layers and layer sorting: 

Create an empty game object as the container object for all background textiles. Make sure to import (drag from Assets --> Game objects) in the layer order! i.e. create the lowest photoshop layer first. 

Then create a new Unity layer called "Background" and assign the layer to the container object. 

* Drag-and-drop

`OnMouseDown` and `OnMouseUp` are only effective for an object with a collider or a GUIText object.

create prefab: drag a GameObject to Project window.

Add a collider as terrain to enable gravity (one of the two objects must have rigidbody component)
set is Trigger to false

Drag console to project view
