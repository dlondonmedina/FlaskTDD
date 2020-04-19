# Test Driven Design and the Web

Before we move into Flask, I want to walk through an exercise of how we might develop a quick web client using the [requests](https://requests.readthedocs.io/en/master/) library and the test driven design approach.

First, I have set up a resource at http://computingconcepts.herokuapp.com that returns status codes based on the request it receives. I built this as a toy to help students experiment with the requests library, and so it does the following:
 
* If it receives a GET request it will respond.
  
* If that GET request is accompanied by a user-agent called 'DICE' then it will return the string 44260953 in the body of the response
  
* The application is set up to handle POST requests at the resource named 'post', so the complete URL should be https://computingconcepts.herokuapp.com/post and if it received a POST request it will check the data. If the data is ```{'username': 'bobby', 'email': 'bobby@bobby.com'}``` then it will return the string 44261853 in the body of the response. If the data is wrong, it will return "You didn't submit the right data"  

The specifications of my assignment is the following:

1. Build three functions: basic_request(), request_user_agent(), and request_post()
   
2. basic_request takes one parameter: a URL. Uses requests to make a GET request to the URL. It returns -1 if the status code response from the URL is anything but 200, otherwise it returns 200.
   
3. request_user_agent() takes 2 parameters, a URL and a user agent. It uses requests to make a GET request to the URL with the user agent defined. If the URL is bad or the user agent is not set, it will return -1. Otherwise, it will return the content of the response. I know the server above, so I can test this.
   
4. request_post() takes 2 parameters, a URL and a data object (probably dictionary). If the URL is bad or if no data is included, it should return -1. Otherwise, it should return the content of the response. I know the server above so I can test this. 

Just arbitrarily, I want my functions to define a good url as anything that ends with '.com' (Again, this is a toy, so I'm trying to keep it really simple.) I know that I'm doing TDD so I need to start with some tests. I will begin with a setUp() and some tests to test the basic_request() method.

_requester_tests.py_
```python
import unittest
import requester as target

class TestRequester(unittest.TestCase):

   def setUp(self):
      self.url = 'http://computingconcepts.herokuapp.com'
      self.bad_url = 'http://computingconcepts.gopher'
      self.data = {
         'username': 'bobby', 
         'email': 'bobby@bobby.com'
      }
   
   def test_basic_request_bad_url(self):
      response = target.basic_request(self.bad_url)
      self.assertEqual(-1, response)
   
   def test_basic_request_good_url(self):
      response = target.basic_request(self.url)
      self.assertEqual(200, response)
```

I'll need to install the requests library, so I'll do that right now and create the file requester.py. I'll then run my tests. 

```
(env) $ touch requester.py
(env) $ pip install requests
(env) $ python -m unittest requester_tests.py
EE
======================================================================
ERROR: test_basic_request_bad_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/requester_tests.py", line 16, in 
  test_basic_request_bad_url
    response = target.basic_request(self.bad_url)
AttributeError: module 'requester' has no attribute 'basic_request'

======================================================================
ERROR: test_basic_request_good_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "exercises/requester_tests.py", line 20, in
   test_basic_request_good_url
    response = target.basic_request(self.url)
AttributeError: module 'requester' has no attribute 'basic_request'

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (errors=2)
```

Of course we don't have anything in our file, so let's get it set up so that the tests fail the right way.

_requester.py_
```python
import requests

def basic_request(url):
   pass
```

I know this will make my tests fail the right way because there is the correct function to call and it returns nothing. Sure enough, when I run my tests:
```
$ python -m unittest requester_tests.py
FF
======================================================================
FAIL: test_basic_request_bad_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 17, in test_basic_request_bad_url
    self.assertEqual(-1, response)
AssertionError: -1 != None

======================================================================
FAIL: test_basic_request_good_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 21, in test_basic_request_good_url
    self.assertEqual(200, response)
AssertionError: 200 != None

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=2)
```

That's exactly the errors we want, so now we want to get it to pass. I know I want the function to check that the URL is valid first (by our silly arbitrary definition of valid as ending with .com). Second, I want it to use requests to call the valid URL. Third, I want to check the status code and return it if it is 200. If 200 is not the status code or there is a problem with the URL, we should return -1. I've done all that in the code below:

_requester.py_
```python
import requests

def basic_request(url):
   if url[-4:] != '.com':
      return -1

   resp = requests.get(url)
   if resp.status_code == 200:
      return resp.status_code
   
   return -1
```

We run our tests and you'll notice that they hang for a minute on the second test. That's because Python waits on line requests line until it gets a response. The server we're calling is really wimpy, so this might take some time. On my machine, the tests take 10.451 seconds to run.

```
$ python -m unittest requester_tests.py
.200
.
----------------------------------------------------------------------
Ran 2 tests in 10.451s

OK
```

Now we need to move on to our next function. We need to test that request_user_agent first checks the validity of the url. We also need to check that it returns -1 if the user agent is not set, and finally that it returns the content if the user agent is set. I've built the app on heroku specifically to do this. I know that if the user agent is 'DICE' the response will be 44260953. The idea is that you should be able to predict the output based on the input, and that's what you are testing.

_requester_tests.py_
```python
import unittest
import requester as target

class TestRequester(unittest.TestCase):


   def setUp(self):
      self.url = 'https://computingconcepts.herokuapp.com'
      self.bad_url = 'https://computingconcepts.gopher'
      self.data = {
         'username': 'bobby',
         'email': 'bobby@bobby.com'
      }

   def test_basic_request_bad_url(self):
      response = target.basic_request(self.bad_url)
      self.assertEqual(-1, response)
   
   def test_basic_request_good_url(self):
      response = target.basic_request(self.url)
      self.assertEqual(200, response)      

   def test_user_agent_bad_url(self):
      resp = target.request_user_agent(self.bad_url, 'DICE')
      self.assertEqual(-1, resp)
   
   def test_user_agent_no_user_agent(self):
      resp = target.request_user_agent(self.url, None)
      self.assertEqual(-1, resp)
   
   def test_user_agent_success(self):
      resp = target.request_user_agent(self.url, 'DICE')
      self.assertEqual('44260953', resp)
```
I'm also going to quickly add a function to requester.py called request_user_agent() and have it pass so I don't have to go through the errors. We'll see that the tests fail.

```
$ python -m unittest requester_tests.py
..FFF
======================================================================
FAIL: test_user_agent_bad_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 25, in test_user_agent_bad_url
    self.assertEqual(-1, resp)
AssertionError: -1 != None

======================================================================
FAIL: test_user_agent_no_user_agent (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 29, in test_user_agent_no_user_agent
    self.assertEqual(-1, resp)
AssertionError: -1 != None

======================================================================
FAIL: test_user_agent_success (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 33, in test_user_agent_success
    self.assertEqual(44260953, resp)
AssertionError: 44260953 != None

----------------------------------------------------------------------
Ran 5 tests in 5.449s

FAILED (failures=3)
```

So now we need to write the code to make these tests pass. We can copy our url check from basic_request() and maybe add some or logic to it to catch when our user_agent is None. Then we just need to handle the request. Please do take a moment to look at the documentation for requests to figure out how you might pass a user_agent to the request object.

_requester.py_
```python
import requests

def basic_request(url):
   if url[-4:] != '.com':
      return -1


   resp = requests.get(url)

   if resp.status_code == 200:
      return resp.status_code

   return -1

def request_user_agent(url, agent):
   if url[-4:] != '.com' or agent == None:
      return -1
   
   headers = {
      'User-Agent': agent
   }
      
   resp = requests.get(url, headers = headers)

   if resp.status_code == 200:
      return resp.text
   
   return -1
```

What's interesting here is that I ran into an unexpected error. Initially I returned ```resp.content``` but that yielded these results:

```
(env) $ python -m unittest requester_tests.py
....F
======================================================================
FAIL: test_user_agent_success (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 33, in test_user_agent_success
    self.assertEqual(44260953, resp)
AssertionError: 44260953 != b'44260953'

----------------------------------------------------------------------
Ran 5 tests in 9.271s

FAILED (failures=1)
```

I forgot that the data in the ```content``` is returned as bytes, so I had to use the ```text``` property instead. Now all my tests pass:

```
(env) $ python -m unittest requester_tests.py
.....
----------------------------------------------------------------------
Ran 5 tests in 10.985s

OK
```

Now the last bit that we have to do is build our requests_post() function. Once again, I know that I need to test both of the parameters with the wrong data and both parameters with good data. However, instead of using the GET method, I know my function needs to call requests.post() instead. Again since I built the server to run tests against, I can predict the appropriate input and output for my function if it is working correctly. 

_requester_tests.py_
```python
import unittest
import requester as target

class TestRequester(unittest.TestCase):


   def setUp(self):
      self.url = 'https://computingconcepts.herokuapp.com'
      self.bad_url = 'https://computingconcepts.gopher'
      self.data = {
         'username': 'bobby',
         'email': 'bobby@bobby.com'
      }
      self.bad_data = {

      }

   def test_basic_request_bad_url(self):
      response = target.basic_request(self.bad_url)
      self.assertEqual(-1, response)
   
   def test_basic_request_good_url(self):
      response = target.basic_request(self.url)
      self.assertEqual(200, response)      

   def test_user_agent_bad_url(self):
      resp = target.request_user_agent(self.bad_url, 'DICE')
      self.assertEqual(-1, resp)
   
   def test_user_agent_no_user_agent(self):
      resp = target.request_user_agent(self.url, None)
      self.assertEqual(-1, resp)
   
   def test_user_agent_success(self):
      resp = target.request_user_agent(self.url, 'DICE')
      self.assertEqual('44260953', resp)

   def test_post_bad_url(self):
      resp = target.request_post(self.bad_url, self.data)
      self.assertEqual(-1, resp)
   
   def test_post_no_data(self):
      resp = target.request_post(self.url, None)
      self.assertEqual(-1, resp)
   
   def test_post_bad_data(self):
      data = {'username': 'bad', 'email':'bad'}
      resp = target.request_post(self.url, data)
      self.assertEqual("You didn't submit the right data", resp)
   
   def test_post_good_data(self):
      resp = target.request_post(self.url, self.data)
      self.assertEqual('44261853', resp)
```

Then we add the request_post() function to requester.py and run our tests:

```
$ python -m unittest requester_tests.py
..FFFF...
======================================================================
FAIL: test_post_bad_data (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 49, in test_post_bad_data
    self.assertEqual("You didn't submit the right data", resp)
AssertionError: "You didn't submit the right data" != None

======================================================================
FAIL: test_post_bad_url (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 40, in test_post_bad_url
    self.assertEqual(-1, resp)
AssertionError: -1 != None

======================================================================
FAIL: test_post_good_data (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 53, in test_post_good_data
    self.assertEqual('44261853', resp)
AssertionError: '44261853' != None

======================================================================
FAIL: test_post_no_data (requester_tests.TestRequester)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/user1/SU/everyday/week3/exercises/requester_tests.py", line 44, in test_post_no_data
    self.assertEqual(-1, resp)
AssertionError: -1 != None

----------------------------------------------------------------------
Ran 9 tests in 0.733s

FAILED (failures=4)
```

Excellent! Again it's failing like we want it to fail. Again, I've set up my tests to fail in a particular way so that when they pass, I know the code is doing exactly what I want it to be doing. I'm going to build out the code next. Again, I leave you to read up on how to send data with requests. It uses the post() method. 

_requester.py_
```python
import requests

def basic_request(url):
   if url[-4:] != '.com':
      return -1


   resp = requests.get(url)

   if resp.status_code == 200:
      return resp.status_code

   return -1

def request_user_agent(url, agent):
   if url[-4:] != '.com' or agent == None:
      return -1
   
   headers = {
      'User-Agent': agent
   }
      
   resp = requests.get(url, headers = headers)

   if resp.status_code == 200:
      return resp.text
   
   return -1

def request_post(url, data):
   if url[-4:] != '.com' or data == None:
      return -1
   url = url + '/post'
   resp = requests.post(url, data = data)

   if resp.status_code == 200:
      return resp.text
   
   return -1 
```

You'll notice that I'm concatenating '/post' to the url so that the right resource is being called by the request. Then we run our tests and we're good to go.

```
(env) $ python -m unittest requester_tests.py
.........
----------------------------------------------------------------------
Ran 9 tests in 2.363s

OK
```

In the next chapter we'll finally be able to get into building our Flask app with TDD. We'll be building a miniature blog over the remaining chapters.