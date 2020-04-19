# Introducing the unittest Module
## A note to begin:
One thing to notice before we go much further is that we have a number of tools running at the same time to do our tests.
   
1. Our Flask server that is launched when we type ```flask run``` in our terminal. This web server runs on your local machine, is not visible to the outside world, and listens at the [IP address](https://en.wikipedia.org/wiki/IP_address) 127.0.0.1 on port 5000. In the URL bar, this will look like ```127.0.0.1:5000```. 
   
2. Our Flask app that the Flask server calls when it receives requests.
   
3. Our *functional_test.py* script, which opens a web browser to send particular requests to the Flask server, and evaluates the server's responses.
I recommend, perhaps, diagramming these for yourself in some fashion if it helps you keep things in order. 

## Unittests
Our last iteration of our app was extremely simple. It didn't even use any HTML and didn't properly return a web-page; just the string "Hello, Flask!" That's not particularly useful if we're trying to build a little web-app, but it's a start. Now we're moving on to bigger and better things. You'll actually have a proper webpage. Our first step will be to modify our functional test so that it looks for a title property. We'll alo add the story that Percival mentions So let's go to the functional test:
``` python
from selenium import webdriver

browser = webdriver.Firefox()

# Edith (our test) has heard about a cool new online to-do app. She goes
# to check out its homepage
browser.get('http://127.0.0.1:5000') # or http://localhost:5000

# She notices the page title and header mention to-do lists
assert 'To-Do' in browser.title

# She is invited to enter a to-do item straight away

# She types "Buy peacock feathers" into a text box (Edith's hobby
# is tying fly-fishing lures)

# When she hits enter, the page updates, and now the page lists
# "1: Buy peacock feathers" as an item in a to-do list

# There is still a text box inviting her to add another item. She
# enters "Use peacock feathers to make a fly" (Edith is very methodical)

# The page updates again, and now shows both items on her list

# Edith wonders whether the site will remember her list. Then she sees
# that the site has generated a unique URL for her -- there is some
# explanatory text to that effect.

# She visits that URL - her to-do list is still there.

# Satisfied, she goes back to sleep

browser.quit()
```  
*functional_tests.py*

## Comments
There are lot of opinions when it comes to how we add comments to our code. In a traditional CS program, we'd expect students to write extensive comments. This is to help the instructor see what you believe you are doing with a particular line of code or function. Likewise, the practice of writing such comments is useful for you to become conscious of what your code is doing. Comments can also be used as a sort of pseudocode to describe what functions your code will need to accomplish (as in the above example). 

In professional settings, there is a debate as to how many comments are necessary. On the one hand, some argue that your code should be clearly written enough that you don't need any comments. If your code needs comments to explain what's going on, you probably have a problem with your code. They also argue that if you change your code in the future without changing your comments, the comments can become misleading and more of a hinderance for future developers.

On the other hand, some argue that comments provide crucial insight to you and other developers about what the code is supposed to do. They give future developers a sense of what you were thinking when you were writing the code. They are also important if you are writing an API or other code that future developers will extend. Additionally, comments are frequently used to generate documentation. For instance Sphinx and other documentation generation tools can automatically create docs based on the comments.

Beyond all of this, if you're working for a company, that company will likely have its own approach to comments, and probably a majority of your first period there will be reading the code and comments that you will later be working on. 

This is all to say, you'll have to decide how much you want to comment your code based on the project. A general rule of thumb is that if the code doesn't obviously tell you what is happening, it's worth commenting. For example the first comment is probably not needed, but the second is (I also probably wouldn't write that generator just to save a few lines of code anyway):

``` python
# Increment age by 1 
age += 1

n = int(input("How many players? "))
# prompt users for name and age for each player
age_map = {input("Name?"): int(input("Age?")) for i in range(n)}
```

## Back to our Tests
If we start up our Flask server:

```
(env) $ flask run
```

and in another shell run our functional tests, you'll get an error like this:

```
(env) $ python functional_tests.py
Traceback (most recent call last):
  File "functional_tests.py", line 6, in <module>
    assert 'To-Do' in browser.title
AssertionError
```

We are getting the error that we are expecting! However, we want to move over to the unittest framework to make our test a little bit more robust. After all, we'll be building an entire testing suite for our application: every operation we want should be tested. This will be very similar to the test in Percival's book, but before that, take a moment to consider the unittest library.

### unittest
The [unittest library](https://docs.python.org/3/library/unittest.html) is a framework for writing unit tests. 

A unit test is an automated test that checks the functionality of one unit of a particular application. For instance, one unit of our webpage is the title that shows up on the browser tab. A unit test will test that that is as it should be. Likewise, it might test a single function of our application. When writing a unit test, we need to provide all of the conditions or dependencies except the one variable that is being tested. So our browser title test needs to just test that the browser title contains particular content. It shouldn't check that the browser starts up or anything that appears on the page or that the URL is correct. 

A functional test is a test that checks a particular functionality of our application. For instance, a functional test might check that we can input a new item into a list and when we do so the item gets added to the database. Unlike a unit test that might have distinct tests for user input and writing to the database, the functional test tries to see that the components work together properly. 

Another way to think of this: unit tests check a discrete section of code. Functional tests simulate user experience. 

So, now lets update our functional_tests.py file. You'll notice that this is a functional test because it tests a component of our application as a user would encounter it. We'll write unittests later. 

``` python
from selenium import webdriver
import unittest 

class NewVisitorTest(unittest.TestCase):
   
   def setUp(self):
      self.browser = webdriver.Firefox()
   
   def tearDown(self):
      self.browser.quit()

   def test_can_start_a_list_and_retrieve_it_later(self):
      # Call homepage
      self.browser.get('http://127.0.0.1:5000')

      # Old way: 
      # assert 'To-Do' in browser.title

      # Using unittest methods
      self.assertIn('To-Do', self.browser.title)

      self.fail('Finish writing the test!')

      # She is invited to enter a to-do item straight away

      # She types "Buy peacock feathers" into a text box (Edith's hobby
      # is tying fly-fishing lures)

      # When she hits enter, the page updates, and now the page lists
      # "1: Buy peacock feathers" as an item in a to-do list

      # There is still a text box inviting her to add another item. She
      # enters "Use peacock feathers to make a fly" (Edith is very methodical)

      # The page updates again, and now shows both items on her list

      # Edith wonders whether the site will remember her list. Then she sees
      # that the site has generated a unique URL for her -- there is some
      # explanatory text to that effect.

      # She visits that URL - her to-do list is still there.

      # Satisfied, she goes back to sleep

if __name__ in '__main__':
   unittest.main(warnings='ignore')
```

Once again, we want to test it. If you stopped your Flask server, launch it again. Then let's run our functional tests and see the results:

```
(env) $ python functional_tests.py
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVisitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 20, in test_can_start_a_list_and_retrieve_it_later
    self.assertIn('To-Do', self.browser.title)
AssertionError: 'To-Do' not found in ''

----------------------------------------------------------------------
Ran 1 test in 37.913s

FAILED (failures=1)
```
Woo! Our tests are failing as they should be. Notice, we haven't added anything to our app in this chapter, and that's intentional. We now have a test that will pass once our app is returning a page that has the correct title and that will be our next step. For now, it would be a good moment to commit your project and push it to github. 