# Tutorial I

## Create new project

We used the *django-admin.py* tool to create a new project. Use this command in the terminal -

	django-admin.py startproject mysite

It will create a directory named mysite. Directory will have the following structure - 
	mysite/
		manage.py
		mysite/
    		__init__.py
    		settings.py
    		urls.py
    		wsgi.py

*Note : This structure may be different if you have installed any other version of Django.*

Insight to the created project :

* The external mysite directory is actually our original project directory. Its name does not really matter.
* manage.py: This is one of the command line tools for Django. Using this, various projects related projects are done.
* The inner mysite directory is the main application, which is actually a Python package, inside which some basic configuration of our applications are saved.
* mysite / __init __.py:  __init__.py is usually an empty file that indicates that this directory is actually a Python package. It is quite a work to distinguish between common directories and Python packages.
* mysite / settings.py: Application settings are in this file.
* mysite / urls.py: This file contains definitions for all URLs in this application. 
* mysite / wsgi.py: This file is useful when using WSGI Compatible Server. We will look at the duties of this file when we talk about the development.


## Launch the development server

Launching of the application can be done without the need of any other server, as follows -

	python manage.py runserver

The output will be -
	Validating models...

	0 errors found
	May 24, 2013 - 15:50:53
	Django version 1.5, using settings 'mysite.settings'
	Development server is running at http://127.0.0.1:8000/
	Quit the server with CONTROL-C.

Enter the *http://127.0.0.1:8000/* address in the browser,then the home page of Django will be visible.

*Note : If we want, we can easily change the port of the server. For example, if we want the server to run at 8080 ports, then we will use the runserver command with an additional port number -*

	python manage.py runserver 8080


## Setting up the database

If we open mysite / settings.py file in the text editor, we will see that there is a separate section for the database, as -

	DATABASES = {
		'default': {
    		'ENGINE': 'django.db.backends.', # Add 'postgresql_psycopg2', 'mysql', 'sqlite3' or 'oracle'.
        	'NAME': '', # Or path to database file if using sqlite3.
		    # The following settings are not used with sqlite3:
    		'USER': '',
    		'PASSWORD': '',
    		'HOST': '', # Empty for localhost through domain sockets or '127.0.0.1' for localhost through TCP.
    		'PORT': '', # Set to empty string for default.
    		}
	}

Here -

* ENGINE : is the database driver name.
* NAME : is the name of the database. Filename in SQLite.
* USER : Database User Name
* PASSWORD : is the password.
* HOST : is the name of the database server that runs the database. Localhost hosting will be localhost. If implied, Django will use the localhost.
* PORT : database server that is running on the port. Generally it does not give any value.

Example -
	DATABASES = {
		'default': {
    		'ENGINE': 'django.db.backends.mysql', 
    		'NAME': 'student_database', 
    		'USER': 'root',
    		'PASSWORD': 'Pa55w0Rd',
    		'HOST': '', 
    		'PORT': '', 
		}
	}

## Syncdb - Database synchronization

A Django site or project is composed of many small applications. These applications are added to *settings.py* INSTALLED_APPS section. By default, there are many applications added to the list. Many applications store data in the database. So they need to make tables. The sync_b command of *manage.py* does this only for us. We will run in the terminal -

	python manage.py syncdb

*Note: Django will create tables for just the INSTALLED_APPS list of applications. Run syncdb by adding all the applications you need. But after creating a table and then removing it from the application list, the jango will not be removed from that table database.*

## Project vs Application

We have created a project earlier. A project is a group of one or more applications. We share a Django site or over all of its project function in small units. Then we develop different applications for each different feature. Suppose developing the site for a community. You will need -

* Blogs
* Forum
* Photo or Video Gallery

In Java, we will create separate applications for each function. But in Django, all the code works in the same place, i.e. in one place. Django applications are a lot of plug and play. You just add to the settings of the INSTALLED_APPS section of the original application, once syncdb and then add or import routes to urls.py. Due to this type of flexibility, it is fun and less time consuming to work in Django.

## Create own applications 

We will create the application in conjunction with the Official Tutorial of Django. Concept of applications is quite simple. "poll" is the name of the application.
To create an application's structure, use the manage.py startup command -
	python manage.py startapp polls
Then another folder (actually Python package) will be created on project folder. It  looks like follows -
	
	polls/
		__init__.py
		models.py
		tests.py
		views.py

Here -

* _init__.py : Indicates that the directory is a python package
* models.py :  Creates required data models
* tests.py : Will keep test cases for unit testing
* views.py : Django is a little different from the traditional MVC framework views.py. Instead of Controller we use View in Django. In this file views are created.

## MVC vs MTV

Components of the MVC framework are:

* Models: Data Model
* View: What Users See Generally HTML, CSS, JS.
* Controller: Controller model coordinates with view of data.

We will follow MTV (Model, Template, View) pattern in Django. Components of the MTV framework are:

* Model : same as Model in MVC
* View : is where templates are there
* Controller: is replaced by View

## Creating Model

Django's philosophy all application data is in a central model. Now we’ll define your models – essentially, your database layout, with additional metadata.Edit the polls/models.py file so it looks like this: -

	from django.db import models

	class Poll(models.Model):
		question = models.CharField(max_length=200)
		pub_date = models.DateTimeField('date published')

	class Choice(models.Model):
		poll = models.ForeignKey(Poll)
		choice_text = models.CharField(max_length=200)
		votes = models.IntegerField(default=0)
 
The idea is that each model of Django is a class. Models are represented in the database table. The class variable denotes the field or column of the table. Determine the type of these variables in the table column or the data type of the field. In this example CharField contains few characters (or short strings). IntegerField integers (full-length) and DateTimeField are used for day-to-date. Many of these default field types are found in the models module. 
##Use of model

To use the model, first sync the database. In this, Django creates the necessary tables. However, to sync the database, first add the app to *settings.py* INSTALLED_APPS - 
	
	INSTALLED_APPS = (
		'django.contrib.auth',
		'django.contrib.contenttypes',
		'django.contrib.sessions',
		'django.contrib.sites',
		'django.contrib.messages',
		'django.contrib.staticfiles',
    		'polls',
	)

Followed by running this -
	python manage.py syncdb

To understand the real use of the model, open the interactive shell. Like Python's interactive prompt, it gives the opportunity to use the Django API. Use this command to run it -
	python manage.py shell
In the shell, we will test the output by using the line by line code.

First of all, import the class -
	>>> from polls.models import Poll, Choice
It would give output as -
	>>> Poll.objects.all()
		[]
There is nothing added to *polls* yet.

Then create the first poll -
	>>> from django.utils import timezone
	>>> p = Poll(question="What's new?", pub_date=timezone.now())
	>>> p.save()
Django will save the object in the database. Now let's see the various attributes of the object -

	>>> p.id
		1
	>>> p.question
		"What's new?"
	>>> p.pub_date
		datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)
Now let's make some changes -

	>>> p.question = "What's up?"
	>>> p.save()
Now again the list of all poll objects is not seen -

	>>> Poll.objects.all()
		[<Poll: Poll object>]
This way we can easily create and use models. Further, details on the model is explainedgit.
