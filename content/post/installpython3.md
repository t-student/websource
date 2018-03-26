+++
title = "Installing Python on Windows 10"

date = 2018-03-26
lastmod = 2018-03-26
draft = false


tags = ["python"]
summary = "Installing python"


[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-default.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-ocean.png"
caption = "Ocean"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-dark.png"
caption = "Dark"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-forest.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-coffee-playfair.png"
caption = "Coffee theme with Playfair font"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-1950s.png"
caption = "1950s"
+++



I have decided to start doing more coding in Python, so I needed to install it. The title says it all but this is just to record what I did. In brief:

+ install a package manager (equivalent to apt in linux) 
+ use python to install pip (a package manager for python) 
+ install an environment manager so that you can support different sets of dependencies with minimal hassle

I started with the instructions [here](http://docs.python-guide.org/en/latest/starting/install3/win/#install3-windows). They work fairly seemlessly. However, the following is what I did.

## Installing Package Manager

You need to do this with administrator privaleges otherwise it won't work. I used the instructions found [here](https://chocolatey.org/install#installing-behind-a-proxy) that are for installing where you are behind a proxy.

To launch a command prompt with admin privaleges do Start, then type cmd, right click on the command launcher and choose more, then run as an admin.

## Installing Python 3

Again use administrator privaleges to invoke `python -m pip install -U pip` at the command line.

## Pipenv and Virtualenv environment manager

This was a bit fiddly but purely down to the fact that the default install relies only on user privaleges. You end up with an error `'pipenv' is not recognized as an internal or external command` because the path is not updated.

Once complete, run `py -m site --user-site` which reports a directory path. It will be something like `C:\Users\mjones\AppData\Roaming\Python\Python36\site-packages`. What you need to do is to add a directory to the `PATH` environment variable that is the same as this user path but ends with `Scripts` rather than `site-packages`.

Pipenv manages library dependencies on a per project basis. You use `pipenv` instead of `pip`. For example, to install the `requests` package, change into your project’s directory (or just an empty directory for this tutorial) and run:

```
cd myproject
pipenv install requests
```

Pipenv will install the requests library and create a Pipfile for you in your project’s directory. The Pipfile is used to track which dependencies your project needs in case you need to re-install them, such as when you share your project with others. For more info, see [here](http://docs.python-guide.org/en/latest/dev/virtualenvs/#virtualenvironments-ref)

`virtualenv` is a tool to create isolated Python environments. `virtualenv` creates a folder which contains all the necessary executables to use the packages that a Python project would need. 




