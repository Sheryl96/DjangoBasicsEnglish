#Tutorial II

##Introduction to Django admin 

Among Django’s popular features, its Admin app is quite popular. This makes any site administration much easier. In thither is a nice interface created to do all the data operations (CRUD == Create Read Update Delete) without any coding. You can use the interface to input new data in the database, view, modify or delete old data easily. 

##Creating Admin application 

The Django admin app can be found in the django.contrib.admin package. So we need to add this application to the settings.py's INSTALLED_APPS section. We saw the example of how to do this work in Tutorial I. 

Admin App provides several data models for its use. So before using the application we need to run the syncdb command -

	python manage.py syncdb

At the end of this process, you can find a super user prompt. Running this command will make all the tables in Django Admin app.

Now we’ll have to add these definitions of Django admin app in the mysite / urls.py file. This is specify the URL for accessing the admin app.

This is done as follows in url.py - 

	from django.conf.urls import patterns, include, url

	from django.contrib import admin
	admin.autodiscover()
	
	urlpatterns = patterns('',
	url(r'^admin/', include(admin.site.urls)),
	)

Django has a variable called urlpatterns in urls.py, in which we define all URLs. We can see more details from how this works, from the Django API.

We've added our definitions to these urlpatterns as - 

	url(r'^admin/', include(admin.site.urls))

This means if URL is requested for admin, we can import the required url in our urlpatterns using the *include* easily. Then Django admin defines admin.site.urls

##Run development server

To run the admin app, we have to launch the development server. The command has been shown -
	python manage.py runserver

To run the development server, go through this URL in browser - 

	http://127.0.0.1:8000/admin/

If you have done everything right, you will get an interface like this -

![admin01](https://cloud.githubusercontent.com/assets/20215525/25672958/5ed2b610-3053-11e7-8f23-64ca08242c52.png)

And if such a screen does not appear to show an error message again, check the steps again. 

##Login to admin

After adding sync admin to Django admin app, we created a super user. To access the admin panel, use that account's username and password. If a super user is not created at that time or if you need a new account for some reason, then run this command to create a new super user - 
	python manage.py createsuperuser
After logging in, we will get a screen like this -

![admin02](https://cloud.githubusercontent.com/assets/20215525/25673029/93100978-3053-11e7-81ba-2c3ae6566695.png)


##Add own models

All admin code related applications should be kept in admin.py. In this, we can tell the admin application what we want to do with our application in the admin panel.

The poll model for our voting needs the Admin Interface. So we will use this code by creating polls / admin.py file -

	from django.contrib import admin
	from polls.models import Poll

	admin.site.register(Poll) 

In this code we actually tell the Django admin to register our poll model on the admin site. 


##Summary 

What we have seen so far is how easily our data model can be used from the Django admin. Django admin is also quite customisable. Using various options, it can be arranged according to your own wish. 