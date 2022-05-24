# Programming Concepts

# Functions / Methods

Functions are blocks of code that can be called from other parts of the code to perform a specific function.

## Methods vs. Functions

Methods are similar to functions, with the only difference being methods are functions that are contained in an Object / Class; functions are stand-alone and not technically contained in classes.

# Vertical Vs. Horizontal Scaling

<font color="green">Vertical Scaling</font> is when you increase the hardware resources in terms of CPU or RAM. <font color="green">Horizontal Scaling</font> is when you introduce more servers to help the load.

# Clustering

The term <font color="green">clustering</font> simply means a collection of distributed servers (sometimes called <font color="purple">nodes</font> or <font color="purple">members</font>). A cluster's main point is its ability to add or remove <font color="purple">nodes</font> quickly and/or dynamically in order to accomodate incrased traffic or mitigate a <font color="purple">node</font> that has failed or has otherwise become unresponsive.  

## State  

Many people claim to want to go back to their carefree childhood - for example, someone may say they want to go back in time to the summer before 4th grade, specifically when they took a trip to Europe. This snapshot of their life can be considered a <font color="green">state</font> - in this <font color="green">state</font>, they are _not_ a 37 year old going nowhere, they are 9 years old enjoying a vacation (and their mother, who passed away last year, is still alive). This snapshot in time - this <font color="green">state</font> - was a happier time in their life.  If they could just turn back time and return to that <font color="green">state</font> things would be much happier.  

In computer science, this concept of <font color="green">state</font> refers to settings, installed applications, values of variables, running processes, etc for a single point in time. For example, if an emulator 'saves <font color="green">state</font>' when you close it, the next time you open the emulator everything will be exactly the same as when you left it.  

Unlike in life, in the computer science world its quite possible to save <font color="green">state</font> and return to it at any time. This is a common term most often used in reference to virtualized machines.  