# Introduction to Objects

One of the major paradigms in programming is object oriented programming (OOP). This approach to programming attempts to bundle data and functionality into generalized objects. It is worth noting that this approach is more popular in server-side programming and in applications that run on a local machine rather than client side websites. This is because websites cannot rely on every client to have the same data stored, and because the web is sometimes fundamentally described as [stateless](https://en.wikipedia.org/wiki/Stateless_protocol). 

We're discussing a little bit of OOP here because when you use flask later on, you will be instantiating a Flask object, and understanding what that means will help you better understand how flask works. 

An object is a set of data and methods taht are collected together. A good way to think of it, but perhaps not a good way to think of it, is to use a car as an example. A car has a bunch of properties (wheels, seats, an engine, air conditioning, etc.). It also has a bunch of methods, perhaps related to those properties, like driving, honking, braking, etc. The key is that the object defines these properties and methods as existing as a sort of blueprint or template. Then when we use the object in our code, we create an instance with specific values. Here's what our object might look like in Python:

```python
class Car:

   def __init__(self, color, max_speed, horn):
      # These are variables that store the color
      # maximum speed, and sound the horn makes.
      self.color = color
      self.current_speed = 0
      self.max_speed = max_speed
      self.horn = horn
   
   def get_horn(self):
      return self.horn

   def get_color(self):
      return self.color

   def get_speed(self):
      return self.max_speed
   
   def accelerate(self)
      # Increases speed by 1 until max speed is reached
      while self.current_speed <= self.max_speed:
         self.current_speed += 1
```

The first line in the snippet above defines the class using the ```class``` keyword and then the class name. The name Car is arbitrary in the same way variable names are arbitrary. They cannot be reserved words in Python, but they can be any other words. It is conventional to capitalize the first letter of every word in class names. After the class name, we might have a set of parentheses in which we place the name of another object that this one inherits from. Don't worry too much about this, but be aware that it might exist and you'll see it in Flask.  

You'll notice above that the Car object has four properties (or four variables that will hold data): color, current_speed, max_speed, and horn. Likewise, the Car object has four methods (or actions that can be performed): three get methods that return the value stored in each of the properties, and an accelerate method that increments the current_speed property until it reaches max_speed. You've probably encountered methods before. If you recall, there are a bunch of methods we can do to a string: ```my_string.upper()``` is one example. What's happening is this this: my_string is an instance of the ```String``` object, which has a method called ```upper()``` that converts all of the characters to upper case. 


