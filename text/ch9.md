# Getting Flask Set Up and Your First Functional Test
The first functional test checks three things:

* Is the selenium library installed and can we import webdriver
* Is there a server serving listening and responding at localhost on port 5000 **This is different from the Django.**
* Is that server responding with the page_source attribute set to "Flask"

Here is the code:

``` python
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://localhost:5000')

assert 'Flask' in browser.page_source
```
*functional_tests.py*

As I follow along with this, I get this error:
 ```
$ python functional_tests.py
Traceback (most recent call last):
  File "functional_tests.py", line 1, in <module>
    from selenium import webdriver
ModuleNotFoundError: No module named 'selenium'
 ```

Notice that the test failed, but not in the way we expected. Should I have written a separate test that selenium was installed? I could have, but since Python throws an import error anyway, it's not that big of a problem to not have a separate test for it. If you're getting this error as well, then the following should fix things up:

``` bash 
(env) $ pip install selenium 
```
After Selenium is installed, then you should get the expected error message. We should expect a connection error because we haven't set up Flask yet, so there's no server listening. 

# Getting Flask Up and Running
I know I haven't installed Flask in this virtual environment, yet, so I know I'll need to do that first. Django has a CLI to help you build your core site, but since Flask is a lightweight Framework with few moving parts, we don't get that. At the same time, we have far fewer files to deal with and far fewer moving parts, so it's a toss up. Flask is a great tool for smaller projects. 

## Step One: Install Flask
Installing Flask is pretty easy. Using pip package manager we just need to install Flask. **Make sure that your virtual environment is activated**

 ```
 (env) $ pip install Flask
 ```

At this point, it would also be valuable to create a requirements.txt file. This file allows you to easily set up your app on a different machine without having to move all of the dependencies. You will be using the "freeze" command that is build into pip. This command echos the list of dependencies that have been installed in this virtual environment using pip install. Try it out in the terminal and you'll see something like this:
``` bash
(env) $ pip freeze
Click==7.0
Flask==1.1.1
itsdangerous==1.1.0
Jinja2==2.11.1
MarkupSafe==1.1.1
selenium==3.141.0
urllib3==1.25.8
Werkzeug==1.0.0
```
When I installed Flask, I also installed its dependencies (Click, itsdangerous, Jinja2, urllib3, werkzeug). You don't need to do this every time, this was just to show you how pip freeze works. 

Now you'll need to somehow write the output of ```pip freeze``` into a text file called *requirements.txt* You could copy and past from the terminal, but that's too much work. Instead, you can use the > or >> symbol to write the output of a command directly to a file like so:
``` bash
(env) $ pip freeze > requirements.txt
```
If I use the > symbol, it will either create or overwrite the file to the right of the symbol. **NOTICE: if you overwrite data will be lost** This is the correct choice in this case because each time we update our requirements.txt file, we want to overwrite it. If we were appending to a file for some other reason, then we would use >> which either creates or appends to the file to the right of the symbol.

## Step Two: Set Up Your Directory
Understanding where your files live is crucial in programming. If the files are not in the right place or if your directory is misshapen, you'll have lots of problems. Django's CLI tool does much of this for you. In Flask, we're on our own. Right now your folder structure within your flask directory should look like this:
```
|--functional_tests.py
|--geckodriver.log
|--requirements.txt
|--env (Virtual Environment Directory)
|   |--[...]
```
We need to create a directory and three files. We need to create a directory called "app" and a file called superlists.py that are both siblings of functional_tests.py. This means they are direct children of our flask directory. We also need to create two files called __init\__.py and routes.py that is a child of the "app" directory. Here are the commands in the terminal.
``` bash
(env) $ mkdir app && touch superlists.py app/__init__.py app/routes.py
```
You can also enter them as three separate commands if you wish. If this was successful, your flask directory should look like this:
```
|--functional_tests.py
|--geckodriver.log
|--requirements.txt
|--superlists.py
|--app
|   |--__init__.py
|   |--routes.py
|--env (Virtual Environment Directory)
|   |--[...]
```
**If your directory doesn't look exactly like this then go back and check for mistakes before you move on.** Now that those files are all created, you can start coding so that the tests will pass. You will be editing superlists.py, __init\__.py and routes.py. The first is the script that launches the Flask application. 

``` python
from app import app
```
*superlists.py*

The second is the core of the app (for now). 
``` python
from flask import Flask

app = Flask(__name__)

from app import routes
```
*__init\__.py*

The third is a file that manages the routes. 

``` python
from app import app

@app.route('/')
@app.route('/index')
def index():
   return "Hello, Flask!"
```

We'll be discussing all of these later on in great detail. Once you've created all of these files, you'll need to start your Flask app so that you can run your tests again. This will require you to have 2 terminals open. In the first terminal, start the Flask app:
``` bash
(env) $ flask run
```
and in the second terminal (make sure you've navigated to your project directory and activated your virtual environment):
``` bash
(env) $ python functional_tests.py
```
At this point, the functional tests will open a new browser and call the Flask app. As soon as that loads, you can close the browser. In your terminal, you should get no error messages. If that's the case, you've successfully completed this. If not, you'll need to go through and revise your code until it works. Make sure to read any error messages for a clue as to what to fix. 

Now, you'll want to initialize your flask project directory as a git repository. Create a gitignore file that contains at least the following:
```
# Byte-compiled / optimized / DLL files
__pycache__/


# Unit test / coverage reports
.pytest_cache/


# Flask stuff:
db.sqlite3
db.sqlite3-journal
instance/
.webassets-cache

# Environments
env/

# Visual Studio Code
.vscode/
```
*.gitignore*

Then create a new github repository and push your local repository to github. You're done with getting started!