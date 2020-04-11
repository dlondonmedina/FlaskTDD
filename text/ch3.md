# Introduction to Objects

One of the major paradigms in programming is object oriented programming (OOP). This approach to programming attempts to bundle data and functionality into generalized objects. It is worth noting that this approach is more popular in server-side programming and in applications that run on a local machine rather than client side websites. This is because websites cannot rely on every client to have the same data stored, and because the web is sometimes fundamentally described as [stateless](https://en.wikipedia.org/wiki/Stateless_protocol). 

We're discussing a little bit of OOP here because when you use flask later on, you will be instantiating a Flask object, and understanding what that means will help you better understand how flask works. 

An object is a set of data and methods taht are collected together. A good way to think of it, but perhaps not a good way to think of it, is to use a car as an example. A car has a bunch of properties (wheels, seats, an engine, air conditioning, etc.). It also has a bunch of methods, perhaps related to those properties, like driving, honking, braking, etc. The key is that the object defines these properties and methods as existing as a sort of blueprint or template. Then when we use the object in our code, we create an instance with specific values. Here's what our object might look like in Python:

_Car.py_
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
      return self.current_speed
   
   def accelerate(self)
      # Increases speed by 1 until max speed is reached
      while self.current_speed <= self.max_speed:
         self.current_speed += 1
```

The first line in the snippet above defines the class using the ```class``` keyword and then the class name. The name Car is arbitrary in the same way variable names are arbitrary. They cannot be reserved words in Python, but they can be any other words. It is conventional to capitalize the first letter of every word in class names. After the class name, we might have a set of parentheses in which we place the name of another object that this one inherits from. Don't worry too much about this, but be aware that it might exist and you'll see it in Flask.  

You'll notice next that everything below the first line is indented, which means it is part of the block that defines this particular class. The first part of the class definition you'll recognize as a function by the keyword ```def```. ```__init__``` is a special function, and it is called the **constructor**. The constructor is a special method or function that is *automatically called when an object defined by this class is constructed*. I'll explain this in a moment, but for now, remember that it's a special function that's automatically called. In Python, the constructor will always be called ```__init__```. 

You'll notice above that the Car object has four properties (or four variables that will hold data): color, current_speed, max_speed, and horn. Likewise, the Car object has four methods (or actions that can be performed): three get methods that return the value stored in each of the properties, and an accelerate method that increments the current_speed property until it reaches max_speed. You've probably encountered methods before. If you recall, there are a bunch of methods we can do to a string: ```my_string.upper()``` is one example. What's happening is this this: my_string is an instance of the ```String``` object, which has a method called ```upper()``` that converts all of the characters to upper case. Likewise, the Car class has a method called accelerate. When we call ```my_car.accelarate()```, you guessed it, the property ```my_car.current_speed``` is incremented until it reaches ```my_car.max_speed```. 

One other thing you might've noticed is this weird parameter ```self```, which is included in each of the functions. Hold tight and I'll explain that below after we get to creating specific instances of the class.

As I said above, the properties of the class are variables, which means in the class definition, they contain no value. Instead, they are empty boxes that could have values put in them. In the computer, what's happening is these names point to the address in memory (RAM) where that data could be stored. When we use this class to create a specific car (instead of the general Car prototype), we create a spot in memory that contains instructions to do the car methods, and addresses to store the specific properties of *this* car. Let me give you an example to make this clear. I'll show you what this looks like in the Python interpreter first.

```python
>>> from Car import Car
>>> my_car = Car('blue', 1000, 'beep beep')
>>> my_second_car = Car('red', 10, 'honk honk')
>>> print(my_car.get_horn())
beep beep
>>> print(my_second_car.get_horn())
honk honk
>>> 
```

There are a few things to notice. First, I have imported the Car class from a file called Car.py. When I do this, the file is read into memory and ```Car``` becomes a known class in the same way as when you define a function. However, that class is a blueprint and contains no data initially. It's not specific cars, it's the generic model of a car. In the next line ```my_car = Car('blue', 1000, 'beep beep')``` a specific car is made. This is also called **instantiation**. An instance of the Car class is constructed. Here's the process.

1. We begin with the right side of the assignment (```=```) operator and we see ```Car('blue', 1000, 'beep beep')```. This tells the interpreter first to find a class definition called **Car**. Since we imported that before, the interpreter will know about that.
   
2. Immediately a copy of the blueprint is established and the ```__init__``` function is called (the constructor is called). You'll notice that the constructor takes 3 parameters (plus self), and you'll notice we passed three values (blue, 1000, and beep beep) when we called the class. The constructor takes these three values just as any function would take values for its parameters.
   
3. The constructor executes. A variable called ```self.color``` is assigned the value ```'blue'```, ```self.current_speed``` is assigned the value 0, and so on. I'll explain ```self``` after this list of steps.
   
4. After the constructor runs, the copy of the blueprint created in step two now has values stored in its variables. It also has methods. 
   
5. The constructed object that was built on steps 1-4 is stored to memory with the variable named ```my_car``` attached.
   
6. Note: the Car class definition is not changed. It still is a blueprint. ```my_car``` is an instance of Car, or a set of values and methods defined by the Car class and set up by the constructor in step 3. 

The next line repeats these steps for a second instance of Car and stores that second instance to a variable ```my_second_car```. Again, the Car class definition doesn't change, and we have two instances based off of the same class with different values stored their properties. The remaining lines of the example simply show the output of the ```get_horn()``` method for each instance. The function works the same way for both instances returning the value stored in the ```self.horn``` property, but you'll note that the values are unique to the instance. 

### Self

Before we go further, I need to talk a bit about **self**. This often is a tricky concept. First, we need a little review on scope. If you recall, scope defines where a variable is accessible from. For instance, in the following example:

```python
>>> num = 0
>>> def myfun():
...   num = 5
...   return num
>>> print(num)
0
>>> print(myfun())
5
>>> num = myfun()
>>> print(num)
5
```

the first ```num``` is a global variable and is accessible in the global scope. The second ```num``` inside of ```myfun()``` is a local variable and is pointing to a different address in memory than the first ```num```. The following lines prove that those two variables are different. Also, you'll recall that variables are only accessible in the scope in which they are defined. The same is true with classes. Class properties are variables that are accessible anywhere in the class. Each method might also have local variables that are accessible only within that method. When the class is instantiated, the class properties are populated with values. 

So where does ```self``` come in? ```self``` references **this** current instance of the class. So, in the car example above, the value stored in ```my_car```'s ```self.color``` would be 'blue'. There are a few things: each method within a class must at least take ```self``` as a parameter. This is how the method gains access to the instance's properties and methods. Second, and this is really important, local variables disappear when they go out of scope. Class variables identified by ```self``` remain until the instance of the class is deleted. This means in the above example that when any instance of the car is instantiated, the value for ```self.current_speed``` is 0. When we call ```accelerate()``` for that instance the value for that instance of ```self.current_speed``` is incremented until it reaches the ```self.max_speed```, and it stays at that value until it is changed again or the instance is deleted. It does not revert back to zero after ```accelerate()``` stops running. 

### Visibility

In some programming languages, the notion of scope is taken a bit more seriously, and we can use objects to protect or hide the values stored in various parameters. I'll give you an example that's a bit of a digression, but let's look at the car example in C++:

_car.cpp_
```c++
class Car {
   public:
      Car(string color, int max_speed, string horn) {
         this.color = color;
         this.current_speed = 0;
         this.max_speed = max_speed;
         this.horn = horn;
      }

      string get_horn() {
         return this.horn;
      }

      string get_color() {
         return this.color;
      }

      int get_speed(self) {
         return self.current_speed;
      }
   
      void accelerate() {
         while (this.current_speed <= this.max_speed) {
            this.current_speed++;
         }
      }
   
   private:
      string horn;
      string color;
      int current_speed;
      int max_speed;
};

int main() {
   Car my_car = new Car("blue", 40, 'beep beep');
   cout << "The color is: " << my_car.get_color() << endl;
   // This next line will not work because color is private.
   cout << "The color is: " << my_car.color << endl;
}
```
In the class above, all of my object properties are called "private" properties, which means they cannot be accessed directly from outside of the class. Instead, I use getter methods to return the values. This is useful because you can control how the properties are changed or retrieved. Likewise, methods could be set as private or protected. I'm not going to go into that here, but if you're interested in all of the features of object oriented programming, it is worth reading up on. 

While languages like Java and C++ allow you to make properties and methods private, Python doesn't do that. All properties are accessible anywhere. In practice, if you want a method to be treated as private, you must start its name with an underscore. So to denote ```accelerate()``` as private, the definition line in Python would look like this: ```python def _accelerate(self):```. This doesn't mean it cannot be accessed from outside the class, but it does tell developers that it shouldn't be accessed directly. Instead, maybe you have another method from within the class that can access it. 

We won't be dealing with any of this in this book, but it is useful to know that one of the major reason we use objects, besides to collect data and methods into single units.

### Conclusion

Object oriented programming, or designing your system around a number of classes, is a popular paradigm. I would encourage you to research it further. In the next chapter, I'm going to walk through an example of building a class using Test Driven Development that builds on the prime_factors function from the first chapter. However, for our purposes, we'll be using classes, but we won't be going to far down the OOP path. You will have a better sense now of what's happening when we say ```app = Flask(__name__)```.