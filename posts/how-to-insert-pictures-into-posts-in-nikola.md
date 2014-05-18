<!-- 
.. link: 
.. description: 
.. tags: Tricks, Code
.. date: 2014/05/19 00:19:35
.. title: How to insert images into posts in Nikola
.. slug: how-to-insert-pictures-into-posts-in-nikola
-->

Since it took me a surprisingly long time to solve this simple problem -- it's not in Nikola's [official docs](http://getnikola.com/handbook.html)(where they only have a section on galleries) and my Google search was unfruitful, I thought I'd write it down.

The simple trick is based on the fact that Nikola will automatically copy all contents under `files/` folder to `output/` folder when you run `nikola build`.

So here are the steps:

1. Create a subdirectory in `files/`. Say `regex_pic/` (where I put my images for the previous post on [parsing regex](http://xysun.github.io/posts/regex-parsing-thompsons-algorithm.html)) -- this is more about better practice, since I don't like to clutter my `output/` folder. 

2. Put your images into that folder. 

3. In your post, use regular markdown syntax to reference those images. For example: `![simple image1](/regex_pic/simple1.jpg)` will insert `regex_pic/simple1.jpg`. 

4. `nikola build`. Done.
