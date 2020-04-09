# Types of Tests
In the first chapter we looked at an example of how to use a few different tools to test our programs. We saw how we might write a set of tests first before we write any code, and then write our code so that it passes the unit tests. We do this for a few reasons.

1. Tests force us to think of testable actions our code might perform. This means we need to figure out what specific action or output our code should have before we write it. That's a good practice.

2. Tests force us to consider what conditions should be passing and what conditions should be failing for our code.

3. Tests will tell us immediately when we have written enough code.

4. Tests can help us constrain how many features our code base has. We can only build what we've written tests for.

5. Tests yield less buggy code and cut down on debugging time. We know our code works when it passes the tests.
   
Now does this all mean that TDD will give us perfect code? No, it's intirely possible to write code that is full of bugs that still passes our tests and functions. There are four main categories of tests: unit tests, functional tests, and end-to-end tests. 

An important concept to understand before we dive into these approaches is **white box** and **black box** testing. White box testing is concerned with the inner workings of your application. This means that it tests *how* your functions are doing the work that they are supposed to do. White box testing cares about the code itself. Black box testing, on the other hand, is concerned with whether your application works or not. This is an important distinction, and you might think of it in these terms. Developers are concerned with white box testing because it ensures that their code has minimal bugs and achieves the correct results the way it's supposed to. Business managers are concerned with black box testing because it ensures that the code is meeting the product requirements. 

### Unit Testing
Unit testing is white box testing and involves testing individual units or individual processes in your code *free from their dependencies.* A good way to think about this is that unit testing should test individual funtions--even if that function is never called independently by the user. In the last chapter, I walked through the process of writing tests for a particular function, but let's look at another example. 

Let's say we are working on a project to build word cloud visualizations. These word clouds display the words in a text in a cloud pattern sized by frequency of each word. If we decided not to use the builtin count function in Python, we could go about building our own. This means I need a function that will take a given text, iterate through the words and 
count the frequency of each word. I also don't want to use 
the count function (for this example at least). I also want the function to be able to adjust for case sensitivity or not. Here are pre and post conditions that should be present if this function works correctly:

* The function should take a string as an input.

* It may or may not take a case as a second input, but that second input must be a boolean.

* The function should return some code if the input is wrong.

* The function should return a dictionary with words as keys and their count as values for each word in the text passage. 

This means I'll have seven tests that will, when passing, show me that under the right circumstances, my function is working, and if the wrong input is given to my function, it returns the right error code. Let's write some tests together again. I'll go 

_wordcloud_tests.py_
```python
import unittest
import wordcloud as t

class TestWordCloud(unittest.TestCase):
   def setUp(self):
      self.text = "Peter peter of Pickeled Pepper pepper Pepper"
      self.sensitive = {
         'Peter': 1,
         'peter': 1,
         'of': 1,
         'Pickeled': 1,
         'Pepper': 2,
         'pepper': 2
      }
      self.insensitive = {
         'peter': 2,
         'of': 1,
         'pickeled': 1,
         'pepper': 4
      }
  
   def test_returns_error_code_on_bad_text(self):
      self.assertEqual(t.word_count(1), -1)
   
   def test_returns_error_code_on_bad_case(self):
      self.assertEqual(t.word_count('Pass', 11), -1)
   
   def test_case_sensitive(self):
      self.assertEqual(t.word_count(self.text, True), 
         self.sensitive)
   
   def test_case_insensitve(self):
      self.assertEqual(t.word_count(self.text), 
         self.insensitive)
  
```

There are two options when writing tests and writing production code. You can either write a test, then write code to pass the test, then write another test and get that one to pass, or you can write a set of tests for one particular function and then write code to get them all to pass. In this case, I wrote all of my tests first, and it gives me a sort of todo list. I know I have to write code to get all of these tests to pass, and I should only write that much code. First I'll write the code that gets the first two tests passing.

_wordcloud.py_
```python
def word_count(text, case):
   return -1
```

The first two tests will pass, but the second two tests will fail once I've written this code. Notice that I don't have any logic in this function, but simply a return statement that will pass the test. I like this approach because it is very little code to write, and still it satisfies the test cases. Next I want to write the code to get the case sensitive version to work. 

_wordcloud.py_
```python
def word_count(text, case):

   if isinstance(text, str) and isinstance(case, bool):
      counts = {}
      for word in text.split():
         if word in counts.keys():
            counts[word] += 1
         else:
            counts[word] = 1
         
      return counts
   
   return -1
```

Once again, you can try this out, but all of the tests except for the last one should be passing now. We're making progress, but now I need to think about how I want to implement the case where we don't want to be case sensitive. An easy way to do this is simply set all the words to lower case and then use the same logic as above.

```python
def word_count(text, case):

   if isinstance(text, str) and isinstance(case, bool):
      counts = {}
      if not case:
         text = text.lower()

      for word in text.split():
         if word in counts.keys():
            counts[word] += 1
         else:
            counts[word] = 1
         
      return counts
   
   return -1
```

Now all of my tests are passing, but there's one more feature I want to add. Since word clouds often are used to show the importance of meaningful words, I want to not count words that contain digits or are just string representations of numbers. So if my text included "dinn3r" or "2", those strings should be ignored. I might be tempted to implement this directly, but I need to write tests first, and I'll just add them to the test code I already have (notice I changed the text to be checked):

_wordcloud_tests.py_
```python
import unittest
import wordcloud as t

class TestWordCloud(unittest.TestCase):
   def setUp(self):
      self.text = "Peter peter of Pickeled \
         Pepper pepper Pepper 123 p3pper"
      self.sensitive = {
         'Peter': 1,
         'peter': 1,
         'of': 1,
         'Pickeled': 1,
         'Pepper': 2,
         'pepper': 2
      }
      self.insensitive = {
         'peter': 2,
         'of': 1,
         'pickeled': 1,
         'pepper': 4
      }
  
   def test_returns_error_code_on_bad_text(self):
      self.assertEqual(t.word_count(1), -1)
   
   def test_returns_error_code_on_bad_case(self):
      self.assertEqual(t.word_count('Pass', 11), -1)
   
   def test_case_sensitive(self):
      self.assertEqual(t.word_count(self.text, True), 
         self.sensitive)
   
   def test_case_insensitve(self):
      self.assertEqual(t.word_count(self.text), 
         self.insensitive)
  
   def test_ignores_digits(self):
      self.assertEqual(t.word_count(self.text), 
         self.insensitive)
```

Next, write code to get that test to pass.

_wordcloud.py_
```python
def word_count(text, case):

   if isinstance(text, str) and isinstance(case, bool):
      counts = {}
      if not case:
         text = text.lower()

      for word in text.split():
         # make sure no characters in the word are digits
         if not any(map(word.isdigit, word)):
            if word in counts.keys():
               counts[word] += 1
            else:
               counts[word] = 1
         
      return counts
   
   return -1
```

All of my tests are passing now, so basically I know that my code is working as expected. I haven't done anything to ensure that the user uses the code correctly, but at this point that's okay. There's just one more small change I would like to make and add some documentation. I want to make case a kwarg instead of an arg. I want to do this so I can set a default setting. I want the function to not be case sensitive unless it is asked to be sensitive. Kwargs are cool because you can set a default value for an argument, and that makes it so that we don't need to pass True or False to the function in some of the cases. Only when we want to be case sensitive. I made a small refactor as below with documentation added. 

_wordcloud.py_ 
```python
def word_count(text, case=False):
   """
   This function calculates word frequency in a text.
   This function should not count digit strings.
   
   Args:
      text (str): the text to be searched.
   
   Kwargs:
      case (bool): Should the search be case sensive

   Returns:
      dict. counts keyed by word, or return code::

         -1 -- Error
   """
   if isinstance(text, str) and isinstance(case, bool):
      counts = {}
      for word in text.split():
         if not any(map(word.isdigit, word)):
            if not case:
               word = word.lower()
            if word in counts.keys():
               counts[word] += 1
            else:
               counts[word] = 1
      return counts
   
   return -1
```

### Functional Testing
If unit tests test to see if the code is working correctly, functional tests test to see if a particular functionality of a program is working correctly. This means we are not so concerned with the inner workings of our functions, but instead we are concerned with the functions that an application provides a user. We will cover functional tests as we start building our Flask app, but imagine for now our word cloud application. The user enters a text file and the application creates an image object based on the text. For a functional test, I'd create a mockup of an expected image object based on a particular text, and then pass that text to the function and compare the output with the expected like so:

_functional_tests.py_
```python
import unittest
import wordcloud as t


class TestWordCloudFunctional(unittest.TestCase):

   def setUp(self):
      self.text = "My text ..."
      self.cloud = ''
      with open('img.png', 'rb') as f:
         self.cloud = f.read()
      
   
   def test_can_create_word_cloud(self):
      self.assertEqual(t.make_cloud(self.text), self.cloud)
```

I'm imagining that we'll have to create a function called make_cloud, which probably will call the word_cound and so on. 

### End to End (E2E) Testing

These tests make use of the user interface that you've developed and are like functional tests, but instead of testing one function of your code, they would test a sequence of functions that a user might use. For instance, if I was making a calculator, my E2E test might begin by launching the calculator, perform a series of calculations and then close the calculator. Alternatively, if I was creating a sign-up function for a website, an E2E test would test that through the user interface a new user can be created, saved to whatever storage solution we have for that data, test the user can login, and then log back out again. 

We won't be developing any E2E tests in this class because we'll be focusing on a very small application that can be tested with functional and unit tests.