---
title: "Java Memory Troubles? Tweak the GC"
date: 2013-07-28
type: post
tags: ["JVM", "Java", "stack space", "memory", "heap space"]
---

# Java Memory Troubles? Tweak the GC

### 
tl;dr version

Memory issues? Try `-XX:+UseSerialGC -Xss8m` in your Run Configuration or as command-line parameters.

### 
The Problem

I'm guessing that, like myself, you have found yourself struggling with the following, two memory errors. I will share what I found as solutions to them.

### 
java.lang.OutOfMemoryError: Java heap space

So, you've taken great pains to use only primitive data types, switching from using an `ArrayList<ArrayList<Integer>>` for your data type to  `Integer[][]` even though it's not nearly as easy to work with. You discovered, though, that this just delayed running out of heap space. I tried a solution stated in many other posts on the web and that did seem to help but I can tell you that it didn't fix the problem. Rather, it allowed me to get to the next error.

### 
java.lang.StackOverflowError

I encountered this error for the first time when working on a project with a lot of recursion. This was likely due to what data I was inadvertently putting onto the stack during recursion. However, I recently worked on another project where I couldn't optimize any further the data pushed onto the stack during recursion. Moral of the story is that you should take the time to profile your code and optimize the data which may end up on the stack during recursion. However, after doing your due diligence, you still may need help, though not as much as you might think.

### 
A Likely Solution

After a lot of research, I found the following two StackOverflow.com posts which helped tremendously. First, the solution and then the links. Add the following to your command line when executing your class or add it to your Run Configuration in Eclipse:

`-XX:+UseSerialGC -Xss8m`

#### 
  
-Xss8m

This first switch is the one that intelligent developers worry will be overused. The default for the stack is 512KB. Suggestions have been as high as 1024m for the number component of this parameter. That's 1GB or RAM! If you need that much RAM for your stack then you might be doing it wrong. Something to note is that I had used this on the highly recursive project I mentioned first with 16m as my value. That got things working. Then I went back and got rid of a bunch of Strings in my code which I'd been using for debugging and I no longer needed this flag at all. Moral of the story, don't use it unless you have to so that you are forced to optimize your code.

[StackOverflow.com article](http://stackoverflow.com/questions/2127217/java-stack-overflow-error-how-to-increase-the-stack-size-in-eclipse)

#### 
-XX:+UseSerialGC

It turns out that the `java.lang.OutOfMemoryError: Java heap space` error isn't always what it appears to be. Sure, I was running out of heap space but increasing heap space just allowed bad behavior to continue. A flag which is commonly recommended to deal with this error is `-Xmx1024m`. Again, that's 1GB of RAM. Now, the heap is where your application runs and by default it is given 64MB to work with. So, you could have a valid argument that one day you'll create an application which needs more heap memory. Just not today. It turns out, *and this is key*, **you just might not need more memory at all!**

See, the problem is that Java's garbage collection, while very useful in letting us not worry about destructors and generally functional, can be lazy. So, this is a hint to the GC on how to run. By using this flag, I made it completely unnecessary to use the `-Xmx` flag. Further, this flag meant that I didn't have to have the `-Xss` flag set very high, either. Sure, I'm not writing a [4KB demo](http://awards.scene.org/nominees.php?cat=10) but I'm pretty happy to have everything working in less than 9MB.

[StackOverflow.com article](http://stackoverflow.com/questions/5890616/reading-large-file-in-java-java-heap-space)

### 
Final Words

I hope you find these flags useful. The key thought, though, is that before you put any of the flags on this page to use, inspect your code with careful attention to the data types used. You may just find that you can slim down a bit and use far less memory in doing so. Remember, one day you might be writing code for a mobile or embedded device smaller than a bracelet, i.e. [FitBit](http://www.fitbit.com/), and memory costs money and takes up space.
