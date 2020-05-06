# Your Flask App
We will be building the Superlists app in Flask. The approach and the code will be a bit different, but our tests will be pretty close to the Django Superlists apps discussed in [Percival's book](https://www.obeythetestinggoat.com/book). 

Since Flask is a microframework, we don't have the same CLI to build a new application or feature within our app. In Django, when you run the command ```$ python manage.py startapp lists``` Django creates a brand new app with a bunch of files associated with that application. It includes an admin interface, all of your database models, and your views. This makes building with Django relatively easy and quick, but on the other hand, if you are, say, building an app that doesn't require a database or admin interface or users, then Django gives you a lot of extra stuff that you don't need, still needs to be maintained, and might contain vulnerabilities that will be patched in the future. 

How do we start our app with Flask? 

