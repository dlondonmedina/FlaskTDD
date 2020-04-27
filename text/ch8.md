# Getting Started with Flask

Ok, so I said in the last chapter that we would begin by writing a Flask web application in 7 lines of code, but that's getting a bit ahead of ourselves. There is some setup that we need to do first. Then we have to write a unittest or two. Then once we get our unittest to fail, we'll build our app and get that unittest to pass. Over the next series of chapters we'll be building a simple blogging platform. To do that, we'll be reusing our code, refactoring, and adding to it as we go. Expect to have to make changes to the code that you've already written. 

## Setup the Environment

Our first step is to create a directory where all of our code for this project will go. This will be the project root and nothing for this project should exist outside of this directory. To do this enter your terminal, navigate to a convenient location, and create a directory. For me, I have a directory called ```Projects``` where I put all of my projects. So I navigate to that directory, create a new one called ```blog```, which is totally arbitrary, and then change directories into that new directory:

```
~$ cd Projects
~/Projects/$ mkdir blog && cd blog
~/Projects/blog$
```

Next, I want to set up a virtual environment and version control. If you've never used git before for version control, I recommend this tutorial: [git tutorial](https://git-scm.com/docs/gittutorial). Likewise, if you want to be able to share your code using Github, you can add a remote repository that you can push your changes to. I'll be doing both. I like to do this before I set up the virtual environment because I'm going to tell git to ignore all of the virtual environment files. I'll show you all of the steps first, and then I'll explain each of them.

```
~/Projects/blog$ git init
Initialized empty Git repository in /home/user1/Projects/blog/.git/
~/Projects/blog$ git remote add origin git@github.com:dlondonmedina/blog.git
~/Projects/blog$ touch .gitignore
~/Projects/blog$ python3 -m venv env
~/Projects/blog$ source env/bin/activate
(env) ~/Projects/blog$ pip install flask
Collecting flask
  Downloading https://files.pythonhosted.org/packages/f2/28/2a03252dfb9ebf377f40fba6a7841b47083260bf8bd8e737b0c6952df83f/Flask-1.1.2-py2.py3-none-any.whl (94kB)
    100% |████████████████████████████████| 102kB 1.3MB/s 
Collecting click>=5.1 (from flask)
  Downloading https://files.pythonhosted.org/packages/dd/c0/4d8f43a9b16e289f36478422031b8a63b54b6ac3b1ba605d602f10dd54d6/click-7.1.1-py2.py3-none-any.whl (82kB)
    100% |████████████████████████████████| 92kB 2.9MB/s 
Collecting Werkzeug>=0.15 (from flask)
  Downloading https://files.pythonhosted.org/packages/cc/94/5f7079a0e00bd6863ef8f1da638721e9da21e5bacee597595b318f71d62e/Werkzeug-1.0.1-py2.py3-none-any.whl (298kB)
    100% |████████████████████████████████| 307kB 3.3MB/s 
Collecting itsdangerous>=0.24 (from flask)
  Using cached https://files.pythonhosted.org/packages/76/ae/44b03b253d6fade317f32c24d100b3b35c2239807046a4c953c7b89fa49e/itsdangerous-1.1.0-py2.py3-none-any.whl
Collecting Jinja2>=2.10.1 (from flask)
  Downloading https://files.pythonhosted.org/packages/30/9e/f663a2aa66a09d838042ae1a2c5659828bb9b41ea3a6efa20a20fd92b121/Jinja2-2.11.2-py2.py3-none-any.whl (125kB)
    100% |████████████████████████████████| 133kB 1.6MB/s 
Collecting MarkupSafe>=0.23 (from Jinja2>=2.10.1->flask)
  Using cached https://files.pythonhosted.org/packages/b2/5f/23e0023be6bb885d00ffbefad2942bc51a620328ee910f64abe5a8d18dd1/MarkupSafe-1.1.1-cp36-cp36m-manylinux1_x86_64.whl
Installing collected packages: click, Werkzeug, itsdangerous, MarkupSafe, Jinja2, flask
Successfully installed Jinja2-2.11.2 MarkupSafe-1.1.1 Werkzeug-1.0.1 click-7.1.1 flask-1.1.2 itsdangerous-1.1.0
(env) ~/Projects/blog$ pip freeze > requirements.txt
(env) ~/Projects/blog$ ls
env  requirements.txt
```

1. ```git init``` This initializes the directory as a git repository. This means that I can use git to track the changes made to this directory. What happens is git creates "snapshots" of the directory and each time we ```commit``` a new "snapshot" of the changes from the previous version is saved. If we want to rollback changes, then all that has to happen is for git to restore to the previous snapshot. 
   
2. ```git remote add origin ...``` This links my local repository created in the previous line to a remote repository on Github that I already created. It is an empty repository online, so I just need to add the remote link. You'll notice I'm using ssh instead of https. When I want to copy my local repository to my remote repository, I'll use the ```git push``` command.
   
3. ```touch .gitignore``` I create a .gitignore file. This tells git that some files should be ignored from the version tracking. I'll add content that next and then I'll commit my changes and push to github.
 
4. ```python3 -m venv env``` This uses the venv module, which is part of Python, to create a virtual environment in a directory called ```env``` If you only have Python 3 installed and not Python 2, then your command might be different. On Unix (OSX, Linux) based operating systems you'll probably have Python 2 installed by default.
   
5. ```source env/bin/activate``` This activates the virtual environment. The command is different if you are on Windows (```env\Scripts\activate``` without ```source```).
   
6. ```pip install flask``` This installs Flask in this virtual environment. If you read the output of this command you'll see that flask, click, Werkzeug, itsdangerous, Jinja2, and MarkupSafe are all also installed. These are the dependencies that flask requires. Since we activated the virutal environment in the last step, Flask is only installed to this environment and not globally. 
   
7. ```pip freeze > requirements.txt``` This tells pip to write the list of dependencies that we've installed in this virtual environment to a file called requirements.txt. This will make it easy to reinstall our application elsewhere and make sure our requirements are all correct.
   
8. Finally, I'm listing out the content of my blog directory.

Now we need to get our .gitignore in working order. If you create your repository on Github first and then clone it from there. You can have Github initialize the repository with a README, a License, and a .gitignore files. This is convenient, but I chose not to do that here. So, I need to create the gitignore file. I like to copy the one that Github uses, so I just visit: [Github Python.gitignore](https://github.com/github/gitignore/blob/master/Python.gitignore) and copy it into my .gitignore file.

_.gitignore_
```python
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.nox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.py,cover
.hypothesis/
.pytest_cache/
cover/

# Translations
*.mo
*.pot

# Django stuff:
*.log
local_settings.py
db.sqlite3
db.sqlite3-journal

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
.pybuilder/
target/

# Jupyter Notebook
.ipynb_checkpoints

# IPython
profile_default/
ipython_config.py

# pyenv
#   For a library or package, you might want to ignore these files since the code is
#   intended to run in multiple environments; otherwise, check them in:
# .python-version

# pipenv
#   According to pypa/pipenv#598, it is recommended to include Pipfile.lock in version control.
#   However, in case of collaboration, if having platform-specific dependencies or dependencies
#   having no cross-platform support, pipenv may install dependencies that don't work, or not
#   install all needed dependencies.
#Pipfile.lock

# PEP 582; used by e.g. github.com/David-OConnor/pyflow
__pypackages__/

# Celery stuff
celerybeat-schedule
celerybeat.pid

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# Spyder project settings
.spyderproject
.spyproject

# Rope project settings
.ropeproject

# mkdocs documentation
/site

# mypy
.mypy_cache/
.dmypy.json
dmypy.json

# Pyre type checker
.pyre/

# pytype static type analyzer
.pytype/

# Cython debug symbols
cython_debug/
```

I might go through and remove all of the lines that I know I won't use, but for now, this works. For instance, I know that my virtual environment directory is called ```env/```, so I could remove all of the other ones in the .gitignore. 

Now I'm almost done. I've created my environment, I just need to commit it to git and push it to Github. This is pretty simple:

```
(env) ~/Projects/blog$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore
	requirements.txt

nothing added to commit but untracked files present (use "git add" to track)
(env) ~/Projects/blog$ git add .
(env) ~/Projects/blog$ git commit -m "initial commit"
[master (root-commit) c83fa38] initial commit
 2 files changed, 146 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 requirements.txt
(env) ~/Projects/blog$ git push origin master
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 1.40 KiB | 1.40 MiB/s, done.
Total 4 (delta 0), reused 0 (delta 0)
To github.com:dlondonmedina/blog.git
 * [new branch]      master -> master
 (env) ~/Projects/blog$
```

 1. ```git status``` This tells me what the status is of my git repositiory. You'll see that .gitignore and requirements.txt are not being tracked yet. I like to check this before I commit because sometimes I am not paying perfect attention to all the changes I've made. 

 2. ```git add .``` This adds all of the untracked files and changes to git tracking. This can be undone at this point. You can also specify which files to track by replacing the dot with the file name. 

 3. ```git commit -m "initial commit"``` This line commits the changes I just made to the git repository. In other words, it makes a "snapshot" of the repository in this current condition. The "initial commit" part is an arbitrary message. It should say briefly what the commit changed. I'm a fan of many smaller commits, so these messages should be short. You'll see as we go. 

 4. ```git push origin master``` This finally pushes the changes we made to our local repository up to our remote repository. It says literally, git, please push the changes made on the master branch to the origin (you'll notice we added the remote as origin in the previous series of commands). 

 Okay. Our environment is set up. Now we're ready to turn to our tests. 

## Building our tests and app

 Flask is a web application microframework, so what we'll be testing is the features we've built with Flask. Unlike the functions we've tested so far, we'll be testing an application designed to receive its input from clients on the internet. This means we need to think about how our tests are going to access our application. We have two ways to do this: we could run our web application and then have our tests make requests to the test server that runs the web application, or we can use the built in test client and make our requests through the test client. I'm going to walk through both approaches. For the former, we'll be using the requests library, but in the future, we'll be using selenium to simulate requests made by a web browser. More about that in the next chapter. 

 Our first step is to set up our test directory and our app directory. These will keep our application and our tests contained. Both of these directories should be children of ```blog/``` and siblings of ```env```. We're also going to create a few files to get set up. I've included the output from ls to show you what is in each directory. 
 
 ```
 (env) ~/Projects/blog$ mkdir app tests
 (env) ~/Projects/blog$ touch app.py app/__init__.py 
 (env) ~/Projects/blog$ touch tests/blog_tests.py tests/blog_web_tests.py
 (env) ~/Projects/blog$ ls
app  app.py  env  __pycache__  requirements.txt  tests
(env) ~/Projects/blog$ ls app
__init__.py
(env) ~/Projects/blog$ ls tests
blog_tests.py  blog_web_tests.py
 ```

Now we're going to get our first test setup. The basic Flask application listens at various different routes. If you recall from the chapters on the Internet, when we navigate the web, we visit URLs. A URL includes the domain and subdomain, followed by any parameters that are sent to the server. So, ```https://example.com/index.html``` is a resource at example.com and the specific name of the resource is index.html, which is an html file that the server should be able to return. Web servers are usually setup to serve index.html or index.js or index.php, etc. by default, so a request to ```https://example.com``` by default should return index.html. With web frameworks, our servers, however, are not serving up static html files. Instead, they are building the html dynamically. So, ```https://example.com/posts``` would make a call to the server listening at the address identified by example.com, and it would call the posts method or route. I'd assume based on the name that the posts method would return a web page containing a list of the current posts. We'll discuss this in depth in the next few chapters, but for now, know that our Flask app has specific routes or methods that are registered and will respond if they are called. This will become more apparent as we start. For now, though, lets set up our first tests.

### Using Requests

I'm going to start by writing the tests that would simulate a user making a request across a network. Then I'll create the test that uses the built-in test_client() feature of Flask. To simulate the requests, I'll use the requests library. To do that I install the library. ```(env) ~/Projects/blog$ pip install requests``` and then fix my dependencies with this command ```(env) ~/Projects/blog$ pip freeze > requirements.txt``` 

Now I need to consider what I'm going to test. The easiest way would be to see if the application returns a particular text value. I also need to know what URL the test server uses to host Flask. It happens to be http://127.0.0.1:5000. With that in mind, let's set up our test.

_blog_web_tests.py_
```python
import unittest
import requests

class TestBlog(unittest.TestCase):

   def setUp(self):
      self.url = 'http://127.0.0.1:5000/'

   def test_hello_flask(self):
      resp = requests.get(self.url)
      self.assertEquals('Hello Flask!', resp.text)
```

First, I import the two modules--unittest and requests--and then as we've been doing we set up our test class. 

1. ```setUp(self)``` This method is called automatically by the test runner and it sets a class property called url to the string of the IP address that the Flask test server will be listening at. 

2. ```test_hello_flask(self)``` This test first uses requests to call the Flask server. It then tests that the server responded with the string 'Hello Flask!'. This is an arbitrary string that I will use when I build the app. 

Run our tests and let's see where we're at. There will be many errors, so I'm removing the traceback for efficiency sake.  

```
~/Projects/blog$ python -m unittest tests/blog_web_tests.py 
E
======================================================================
ERROR: test_hello_flask (tests.blog_web_tests.TestBlog)
----------------------------------------------------------------------
Traceback (most recent call last):

... [Removed for brevity] ...

ConnectionRefusedError: [Errno 111] Connection refused

----------------------------------------------------------------------
Ran 1 test in 0.033s

FAILED (errors=1)
```

The ConnectionRefusedError suggests that when requests tried to make a request to the url, the server at the URL refused it. This makes sense because I don't have an app yet. So let me get some things set up so that at least there's an app. 

_app.py_
```python
from app import app
```
This is a simple script that initializes our instance of Flask. It will try to import from our app directory, which will automatically run \_\_init\_\_.py.py in that directory, which will contain our Flask application code. So let's go there next.

_\_\_init\_\_.py_
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_flask():
   return ''
```

1. ```from flask import Flask``` We're importing the Flask class which contains our basic web microframework object.

2. ```app = Flask(__name__)``` This line instantiates Flask as a variable named "app." If you remember from app.py, the app here is the one that is imported in app.py. 

3. ```@app.route('/')``` is a decorator that passes the following function to the route() method. Next chapter we will discuss this in depth. For now, think of this as the way hello_flask() is called when a user requests the route.

I'm not quite ready to run my tests yet. If I were to do so, I would get the same errors because my Flask app isn't running yet. To do this, I need to launch the Flask app and then run my tests. 

```
(env) ~/Projects/blog$ export FLASK_APP=app.py
(env) ~/Projects/blog$ python -m flask run
 * Serving Flask app "app.py"
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

1. ```export FLASK_APP=app.py``` This line creates an environmental variable called FLASK_APP. When we call ```flask run``` on the next line, the program looks for this variable and then runs the assigned script (app.py).

2. In the following output, we can see some information about the server running our Flask app. Don't worry about the WARNING for now, but the key is to not set up flask on a live web server using this method.

Now we open a second terminal, launch our virtual environment, and run our tests again. 

```
(env) ~/Projects/blog$ python -m unittest tests/blog_web_tests.py
F
======================================================================
FAIL: test_hello_flask (tests.blog_web_tests.TestBlog)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "blog/tests/blog_web_tests.py", line 11, in test_hello_flask
    self.assertIn('Hello Flask!', resp.text)
AssertionError: 'Hello Flask!' not found in ''

----------------------------------------------------------------------
Ran 1 test in 0.005s

FAILED (failures=1)

```

This is the kind of failure we're looking for. We'll notice that our flask app is returning an empty string. All we need to do now is change that return and run our tests again.

_\_\_init\_\_.py_
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_flask():
   return 'Hello Flask!'
```

and it fails again. We need to relaunch our flask app. To do this, open the terminal that is running our flask app, and use CTRL+C to stop the process. Then type ```python -m flask run``` again and our tests pass:

```
(env) ~/Projects/blog$ python -m unittest tests/blog_web_tests.py.
----------------------------------------------------------------------
Ran 1 test in 0.006s

OK
```

### Using Test Client

Now, I'm going to build tests using the test_client(). I won't rewrite the Flask app, so I'll just show you the new tests. My first step is to shut down the test server I launched in the last section. 

Just as the test server ran our Flask app, the test_client gives us access to make calls to our app directly without launching a test server. 

_blog_tests.py_
```python
import unittest
from app import app

class TestBlog(unittest.TestCase):

   def setUp(self):
      self.client = app.test_client()
   
   def test_hello_flask(self):
      resp = self.client.get('/')
      self.assertEqual(b'Hello Flask!', resp.data)
```

1. ```def setUp(self)``` In this setUp method, we create a test client. This test client can make calls directly to the app without launching the test server as we did in the last section.

2. ```test_hello_flask()``` This method is only slightly different. Instead of making the request to the app via requests to the test server, this makes a GET request to the test_client. The major difference that is worth noticing is that the data property of the response is a byte-string so we need to compare 'Hello Flask!' as a byte-string to the data. 

Now we can run our tests and see them pass again. 

## Conclusion

One last step that we need to do is commit all this work we did. 

```
(env) ~/Projects/blog$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   requirements.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	app.py
	app/
	tests/

no changes added to commit (use "git add" and/or "git commit -a")
(env) ~/Projects/blog$ git add .
(env) ~/Projects/blog$ git commit -m "built hello flask"
[master 5a061ab] built hello flask
 5 files changed, 36 insertions(+)
 create mode 100644 app.py
 create mode 100644 app/__init__.py
 create mode 100644 tests/blog_tests.py
 create mode 100644 tests/blog_web_tests.py
(env) user1@Desktop1:~/Projects/blog$ git push origin master
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (9/9), 1.08 KiB | 553.00 KiB/s, done.
Total 9 (delta 0), reused 0 (delta 0)
To github.com:dlondonmedina/blog.git
   c83fa38..5a061ab  master -> master
```

At this point we have a very simple application. It returns the string 'Hello Flask!' at one route. However, this is a full web application and we'll be building on this as we work towards a fully functional blog. I'll be showing you how to use both the test_client and requests or selenium to test your application. 