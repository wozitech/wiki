![Python Logo 1](/uploads/logos/python-logo-1.png "Python Logo 1"){.pagelogo}
<!-- TITLE: python -->
<!-- SUBTITLE: Tips, Tricks, Features of python -->

# References
# Flask
This is for python what express is for node; a web fraemwork. It's gained popularity over Django.

* [Reference](https://github.com/pallets/flask)
* [Intro](https://www.fullstackpython.com/flask.html)

# Decorators
Java has them, JavaScript has them, (C# doesn't appear to have them though). But python has them too, decorators - taken from Aspect Oriented Programming (increases modularity with the separation of cross-cutting concerns).

https://sumit-ghosh.com/articles/demystifying-decorators-python/

# virtual environments
When running `pip3 install`, python packages will be installed in the global space. But virtual environments ensures non-pollution of the global space. A virtual environment can be created at a user level (~) or per project level. _Probably not wise doing it on a user level because it will impact all projects for that user._

The following to create a virtual environment within the `.python` _hidden_ folder:
```
python3 -m venv .python
```

When working in a virtual environment, it is typical that the bash prompt will change to give the name of that virtual environment:
```
(.python) [aylingw@linux-pc-home-wozitech-local]$ 
```

# pip
To upgrade pip: `pip3 install --upgrade pip` (sudo to upgrade globally). Note - if ran within a virtual environment, it will only upgrade pip within that virtual environment.

To install a package: `pip3 install flask`.

To install packages given in a `requirements.txt` (python's equivalent of npm's `package.json`: `pip3 install -r requirements.txt`.

To generate a `requirements.txt`: `pip3 freeze > requirements.txt`.

# For node.js developers
https://itnext.io/python-essentials-for-node-js-developers-708bb9487d70

`nodemon` can be used to monitor for changes in python files.

> But I can't get it to run via python3!!!

From the command line:
```
alias python=python3
./node_modules/.bin/nodemon python3 training.py
```