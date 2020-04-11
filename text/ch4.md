# TDD with Objects

In this chapter, I'm going to walk through creating a class using TDD. We'll be building off of material in Chapter 1 and 2, so make sure you've read that carefully. 

At the end of Chapter 1, we had a set of tests that looked like this:

_prime_factors_tests.py_
```python
import unittest
import prime_factors as target

class TestPrimeFactors(unittest.TestCase):
  
   def test_bad_input(self):
      self.assertIsNone(target.prime_factors("apple"))

   def test_returns_list(self):
      l = target.prime_factors(315)
      self.assertIsInstance(l, list)

   def test_returns_correct_list(self):
      l = target.prime_factors(315)
      self.assertEqual([3, 5, 7], l)
```

and we had created a function that we developed to pass those tests.

_prime_factors.py_
```python
import math 

def prime_factors(n):
   if isinstance(n, int):
      out = []
      if n % 2 == 0:
         out.append(2)
         while n % 2 == 0:
            n = n / 2

      for i in range(3, int(math.sqrt(n)) + 1, 2):
         if n % i == 0:
            out.append(i)
            while n % i == 0:
               n = n / i

      if n > 2:
         out.append(n)
      return out

   return None
```

I'm going to build a class that uses prime_factors as a method. I'm also going to create a second method that finds the factorial value of a given number (factorial is 1 * 2 * 3 ... * n-1 * n). Next, calculating prime_factors and factorials for very large values is processor intensive, so let's make our calculator have a couple dictionaries that contain the key/value pairs of calculated numbers.

So we start with our tests. I want to change my file name first to better reflect what we're doing. Then the first thing we need to do is change how we import our calculator. We also need to set up a test for our constructor (the ```__init__``` method). Let's start with those two steps. While we're at it, we're going to modify our existing tests to call object methods instead of freestanding functions.

```python
import unittest
# Import the class definition
from Calculator import Calculator

class TestCalculator(unittest.TestCase):

   def setUp(self):
      self.calc = Calculator()
   
   def test_calculator_constructor(self):
      expected = ({}, {})
      actual = (self.calc.prime_factor, \
               self.calc.factorial)

   def test_bad_input(self):
      self.assertIsNone(self.calc.prime_factors("apple"))

   def test_returns_list(self):
      l = self.calc.prime_factors(315)
      self.assertIsInstance(l, list)

   def test_returns_correct_list(self):
      l = self.calc.prime_factors(315)
      self.assertEqual([3, 5, 7], l)
```

When we run our tests, since we haven't touched our code, we should run into error messages. I'm expecting an import error and a bunch of name errors. Let's see if we get that:

```
(env) $ python -m unittest calculator_tests.py 
E
======================================================================
ERROR: calculator_tests (unittest.loader._FailedTest)
----------------------------------------------------------------------
ImportError: Failed to import test module: calculator_tests
Traceback (most recent call last):
  File "/usr/lib/python3.6/unittest/loader.py", line 153, 
  in loadTestsFromName
    module = __import__(module_name)
  File "exercises/calculator_tests.py", line 3, in <module>
    from Calculator import Calculator
ModuleNotFoundError: No module named 'Calculator'


----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (errors=1)

```

That's exactly the error I was expecting, so to fix this we can go about renaming our file for our code. I changed the name prime_factors.py to Calculator.py. Now my expectation is that there will be a name error because there's no such thing as a Calculator class. Run the tests again and here's the result:

```
$ python -m unittest calculator_tests.py 
E
======================================================================
ERROR: calculator_tests (unittest.loader._FailedTest)
----------------------------------------------------------------------
ImportError: Failed to import test module: calculator_tests
Traceback (most recent call last):
  File "/usr/lib/python3.6/unittest/loader.py", line 153, 
  in loadTestsFromName
    module = __import__(module_name)
  File "exercises/calculator_tests.py", line 3, in <module>
    from Calculator import Calculator
ImportError: cannot import name 'Calculator'


----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (errors=1)

```

Once again, expected error achieved. Let's fix that by modifying my code a bit so that the prime_factors function becomes a method of Calculator. 

_Calculator.py_
```python
import math 

class Calculator:

   def __init__(self):
      self.prime_factor = {}
      self.factorial = {}
   
   def prime_factors(self, n):
      if isinstance(n, int):
         out = []
         if n % 2 == 0:
            out.append(2)
            while n % 2 == 0:
               n = n / 2

         for i in range(3, int(math.sqrt(n)) + 1, 2):
            if n % i == 0:
               out.append(i)
               while n % i == 0:
                  n = n / i

         if n > 2:
            out.append(n)
         return out

      return None


```

I'm expecting a number of errors and failed tests now, but I believe that two of the tests will pass. ```test_bad_input``` and ```test_returns_list``` should still pass, but I'll get errors for the class properties and the list comparison should fail. 

```
(env) $ python -m unittest calculator_tests.py 
.EF.
======================================================================
ERROR: test_calculator_constructor (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 13, in 
  test_calculator_constructor
    self.calc.factorial)
AttributeError: type object 'Calculator' has no attribute 'factorial'

======================================================================
FAIL: test_returns_correct_list (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 24, in 
  test_returns_correct_list
    self.assertEqual([3, 5, 7], l)
AssertionError: Lists differ: [3, 5, 7] != []

First list contains 3 additional elements.
First extra element 0:
3

- [3, 5, 7]
+ []

----------------------------------------------------------------------
Ran 4 tests in 0.001s

FAILED (failures=1, errors=1)

```

Again, this is good. Let's now add a constructor and adjust our function and see if we can get these tests to pass. 

```python
import math 

class Calculator:

   def __init__(self):
      self.prime_factor = {}
      self.factorial = {}
   
   def prime_factors(self, n):
      if isinstance(n, int):
         out = []
         if n % 2 == 0:
            out.append(2)
            while n % 2 == 0:
               n = n / 2

         for i in range(3, int(math.sqrt(n)) + 1, 2):
            if n % i == 0:
               out.append(i)
               while n % i == 0:
                  n = n / i

         if n > 2:
            out.append(n)
         return out

      return None
```

You'll notice that I added a constructor to my class and added ```self``` as a parameter for ```prime_factors()```. The constructor initializes the two class properties to empty dictionaries. At this point, all of my tests should pass.

```
(env) $ python -m unittest calculator_tests.py 
....
----------------------------------------------------------------------
Ran 4 tests in 0.000s

OK
```

Great! Now we might be tempted to add the extra functionality directly to our class and move forward, but we must obey the testing goat. We have to start by writing our tests. First, we need to do similar tests for our new method called factorials. It should test that the method returns ```None``` if it is passed a value that is not an integer. It should also test that the correct value is returned for a given integer. 

Second, we need a tests that make sure each of the dictionaries are being constructed in the right way as the classes methods are called. Let's see if we can get those tests written. 

```python
import unittest
# Import the class definition
from Calculator import Calculator

class TestCalculator(unittest.TestCase):

   def setUp(self):
      self.calc = Calculator()
   
   def test_calculator_constructor(self):
      expected = ({}, {})
      actual = (self.calc.prime_factor, \
               self.calc.factorial)

   def test_bad_input(self):
      self.assertIsNone(self.calc.prime_factors("apple"))

   def test_returns_list(self):
      l = self.calc.prime_factors(315)
      self.assertIsInstance(l, list)

   def test_returns_correct_list(self):
      l = self.calc.prime_factors(315)
      self.assertEqual([3, 5, 7], l)

   def test_factorials_bad_input(self):
      self.assertIsNone(self.calc.factorials("apple"))
   
   def test_factorials_good_input(self):
      l = self.calc.factorials(5)
      self.assertEqual(120, l)
   
   def test_memory_prime_factors(self):
      calc = Calculator()
      calc.prime_factors(315)
      calc.prime_factors(9)
      expected = {315: [3, 5, 7], 9: [3]}
      self.assertDictEqual(calc.prime_factor, expected)
   
   def test_memory_factorial(self):
      calc = Calculator()
      calc.factorials(4)
      calc.factorials(5)
      expected = {4: 24, 5: 120}
      self.assertDictEqual(calc.factorial, expected)
```

We added four new tests. A couple things to notice: first, the memory tests create new instances of the Calculator class. I did this because I wanted to isolate that instance and see if the memory for that one instance was working without any of the other tests altering the state of the instance. ```calc != self.calc``` in these cases. Second, I'm using ```assertDictEqual``` which is a new feature of unittest as of python 3.1, and it allows us to compare equality of dictionaries. Of course, all these should have errors (not fail) because factorials doesn't exist yet. 

```
(env) $ python -m unittest calculator_tests.py 
..EEEF..
======================================================================
ERROR: test_factorials_bad_input (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 27, in test_factorials_bad_input
    self.assertIsNone(self.calc.factorials("apple"))
AttributeError: 'Calculator' object has no attribute 'factorials'

======================================================================
ERROR: test_factorials_good_input (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 30, in test_factorials_good_input
    l = self.calc.factorials(5)
AttributeError: 'Calculator' object has no attribute 'factorials'

======================================================================
ERROR: test_memory_factorial (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 42, in test_memory_factorial
    calc.factorials(4)
AttributeError: 'Calculator' object has no attribute 'factorials'

======================================================================
FAIL: test_memory_prime_factors (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 38, in test_memory_prime_factors
    self.assertDictEqual(calc.prime_factor, expected)
AssertionError: {} != {315: [3, 5, 7], 9: [3]}
- {}
+ {9: [3], 315: [3, 5, 7]}

----------------------------------------------------------------------
Ran 8 tests in 0.001s

FAILED (failures=1, errors=3)
```

Good, so let's add a function called factorials and see that we get at least three failures. Which means we need to modify our code:

_Calculator.py_
```python
import math 

class Calculator:

   def __init__(self):
      self.prime_factor = {}
      self.factorial = {}
   
   def prime_factors(self, n):
      if isinstance(n, int):
         out = []
         if n % 2 == 0:
            out.append(2)
            while n % 2 == 0:
               n = n / 2

         for i in range(3, int(math.sqrt(n)) + 1, 2):
            if n % i == 0:
               out.append(i)
               while n % i == 0:
                  n = n / i

         if n > 2:
            out.append(n)
         return out

      return None
   
   def factorials(self, n):
      pass
```

Run our tests:

```
(env) $ python -m unittest calculator_tests.py 
...FFF..
======================================================================
FAIL: test_factorials_good_input (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 31, in test_factorials_good_input
    self.assertEqual(120, l)
AssertionError: 120 != None

======================================================================
FAIL: test_memory_factorial (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 45, in test_memory_factorial
    self.assertDictEqual(calc.factorial, expected)
AssertionError: {} != {4: 24, 5: 120}
- {}
+ {4: 24, 5: 120}

======================================================================
FAIL: test_memory_prime_factors (calculator_tests.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/calculator_tests.py", line 38, in test_memory_prime_factors
    self.assertDictEqual(calc.prime_factor, expected)
AssertionError: {} != {315: [3, 5, 7], 9: [3]}
- {}
+ {9: [3], 315: [3, 5, 7]}

----------------------------------------------------------------------
Ran 8 tests in 0.001s

FAILED (failures=3)
```

Outstanding! So to get the tests to pass, I need to do a few things. I need to first build the factorials method. Then I need to modify the two methods in the class to add values to the class properties that are keeping track. We might also add a way to make these methods return the stored value rather than run the calculation so as to save processor power. Think of it like a cache. I'll do all of this at once for now, but you would probably want to take each of these as a single step if you were doing very strict TDD. 

Here's what I came up with:

```python
import math 

class Calculator:

   def __init__(self):
      self.prime_factor = {}
      self.factorial = {}
   
   def prime_factors(self, n):
      if isinstance(n, int):
         out = []
         if n in self.prime_factor.keys():
            return self.prime_factor[n]
         
         original = n 
         if n % 2 == 0:
            out.append(2)
            while n % 2 == 0:
               n = n / 2

         for i in range(3, int(math.sqrt(n)) + 1, 2):
            if n % i == 0:
               out.append(i)
               while n % i == 0:
                  n = n / i

         if n > 2:
            out.append(n)
         
         self.prime_factor[original] = out 

         return out

      return None
   
   def factorials(self, n):
      if not isinstance(n, int):
         return None 
      
      out = 1
      original = n 
      while n > 1:
         if n in self.factorial.keys():
            out *= self.factorial[n] # clever
            n = 1
         else:
            out *= n
            n -= 1
      
      self.factorial[original] = out 
      
      return out 
```

You'll notice that I adjusted both methods to check to see if we'd already calculated the factorials or the prime_factors of n, and if so, we return the cached value. Otherwise, we run the calculation. Also, I set up the functionality to store the value in the cache. There's a clever bit on the line with the comment #clever. This will short circuit our while loop if we've already calculated a value for n that is less than the original parameter. This helps because if we are calculating 5 factorial, we have to do 5 * 4 * 3 * 2 * 1, but if we've already calculated 3! and have it stored, then once what happens is we calculate 5 * 4  and when we hit n == 3, then the method looks up our stored result for 3!, which is 6, so the calculation becomes 5 * 4 * 6 and the result is returned without recalcuating 3 * 2 * 1. This matters if our number is 1000 and we've already calculated 960!. It would save us 960 iterations in our loop. 

Here's the results of our unit tests:

```
(env) $ python -m unittest calculator_tests.py 
........
----------------------------------------------------------------------
Ran 8 tests in 0.000s

OK
```

I'd say that's not too bad for a days work. Of course, I could have gone through all of the steps and written one test at a time, but hopefully you're getting the hang of it. In a larger application I might be a bit more careful. 

### Conclusion

So by now you should have a sense of what a class is and how we might build a class using a TDD approach. If you want extra practice, you might do this kind of refactoring with the function from Chapter 2. If you're excited about objects, [Python 4 Everybody](https://books.trinket.io/pfe/14-objects.html) has a great series of lessons on Objects in Python. 
