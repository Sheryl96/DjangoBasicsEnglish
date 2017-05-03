#Tutorial III

## Introduction to Views

As we have seen in the previous discussion, Django is an MVT (Model View Template) framework. In this tutorial we will work with *view*. Each view in Django is like a page. It performs a specific task and usually renders a template at the end.

For example, consider the poll app - 

* The list of all the polls will be in a page.
* The details of the selected poll will be on a page. There will be questions and options for voting.
* Result page will be displayed where a poll result will be displayed 
* On another page user can view the submitted result. 

##Creating views

So let's write our first view and see how it works. Open the `polls \ views.py` and enter the code below - 

	from django.http import HttpResponse

	def index (request):
    	return HttpResponse ("Hello, world. You're at the poll index.")


We will create a file named `urls.py` in the `poll` directory and add this code-


	from django.conf.urls import patterns, url

	from polls import views

	urlpatterns = patterns ('',
    	url (r '^ $', views.index, name = 'index')
	)

*Note : Regular expressions can be used to define a specific pattern URL instead of a fixed URL.*

We added URLconfig file to our own `urls.py` file for our own applications. Now turn it on to the original URLconfig. We saw that all the URLs in the project 'mysite / urls.py` were in the URLconfig. There we imported the URLconfig for Admin App. Now we will also import the URLconfig for our application so that our URLconfigures are loaded while running Django. If that happens, then Django will know exactly which URL will have to run any view of our application.

Now open the file 'mysite / urls.py` and add the following code - 

	from django.conf.urls import patterns, include, url

	from django.contrib import admin
	admin.autodiscover ()

	urlpatterns = patterns ('',
    	url (r '^ polls /', include ('polls.urls')),
    	url (r '^ admin /', include (admin.site.urls)),
	)
	
Here we are going to tell Django to have the 'poll' at the beginning of the URL, to load the URLconfig from the `polls.urls` module. 

##Add some more views 

Always open the 'polls / views.py` file and add the following lines -

	def detail (request, poll_id):
    	return HttpResponse ("You're looking at poll% s."% Poll_id)

	def result (request, poll_id):
    	return HttpResponse ("You're looking at the results of poll% s."% Poll_id)

	def vote (request, poll_id):
    	return HttpResponse ("You're voting on poll% s."% Poll_id)
    	
 Here we add 3 new views - detail, results, vote. Now we will add them to URLconfig. `Polls / urls.py`, so that the contents of the file are as follows -
 
 
	from django.conf.urls import patterns, url

	from polls import views

	urlpatterns = patterns('',
		# ex: /polls/
		url(r'^$', views.index, name='index'),
		# ex: /polls/5/
		url(r'^(?P<poll_id>\d+)/$', views.detail, name='detail'),
		# ex: /polls/5/results/
		url(r'^(?P<poll_id>\d+)/results/$', views.results, name='results'),
		# ex: /polls/5/vote/
		url(r'^(?P<poll_id>\d+)/vote/$', views.vote, name='vote'),
	)
	
Now visit browser at - 
	'http://localhost:8000/polls/34` 
 
Then visit -
	`http://localhost:8000/polls/34/results/` 
				or
	 `http://localhost:8000/polls/34/vote/` 
In order to get output from 'results () `and` vote ()', respectively.

*Note here that we used regular expressions to indicate URL patterns. For more information about regular expressions, you can visit Wikipedia - <a href="http://en.wikipedia.org/wiki/Regular_expression"> http://en.wikipedia.org/wiki/Regular_expression </a> .* 

##Creating an Effective View  

The previous views were not actually doing anything. Just one line text as output. In 'views', write the following -

	from django.http import HttpResponse

	from polls.models import Poll

	def index(request):
		latest_poll_list = Poll.objects.all().order_by('-pub_date')[:5]
		output = ', '.join([p.question for p in latest_poll_list])
		return HttpResponse(output)
    	
Views have functions that returns `HttpResponse` or Http404 etc. Thus view is responsible to display directly in the browser.
There’s a problem here, though: the page’s design is hard-coded in the view.  If you want to change the way the page looks, you’ll have to edit this Python code. So let’s use Django’s `template` system to separate the design from Python by creating a template that the view can use.

## Templates - Basic 

The template's contents are mostly static, which do not change. There are some dynamic partitions that are programmable. When template is loaded, template engine is provided with information for these dynamic parts. Template Engine render the whole content based on that information.

For example, we need to show a message with our user name on a page. In that case, the name will be changed repeatedly, the rest of the message is the same. We can easily do this using template. The template may be -

	Hello {{ username }}, thank you for visiting!  
	
Here {{username}} is dynamic.
Suppose a user's name is 'Maya', we will tell the template that the value of the username will be 'Maya', then we get the output -

	Hello Maya, thank you for visiting! 


Using templates, we do not have to deal with Python codes. Backend and front-end designs can be easily separated. With the advantage of template.


## Depth into django template 

The templates in Django are in the `templates` directory of the application. We create a folder named `templates` in the` poll` directory. In this directory again we create another directory called `poll` for our templates. Here we create a file named `index.html`. The folder path will be - `polls / templates / polls / index.html` Template will contain the following - 

	{% if latest_poll_list %}
		<ul>
		{% for poll in latest_poll_list %}
    		<li><a href="/polls/{{ poll.id }}/">{{ poll.question }}</a></li>
		{% endfor %}
		</ul>
	{% else %}
		<p>No polls are available.</p>
	{% endif %}


Here {{poll.id}} `and` {{poll.question}} 'are dynamic contents. The view for this template can be modified as -

	from django.http import HttpResponse
	from django.template import Context, loader

	from polls.models import Poll

	def index(request):
		latest_poll_list = Poll.objects.all().order_by('-pub_date')[:5]
		template = loader.get_template('polls/index.html')
		context = Context({
    		'latest_poll_list': latest_poll_list,
		})
		return HttpResponse(template.render(context))

    	
To render the template in this example, following is written in `views` -
 
	from django.shortcuts import render

	from polls.models import Poll

	def index(request):
		latest_poll_list = Poll.objects.all().order_by('-pub_date')[:5]
		context = {'latest_poll_list': latest_poll_list}
		return render(request, 'polls/index.html', context)

 
 
## 404 Error Page 

The web would show http error of 404. This means content is not available. To get rid of this error, we will have to run the following in `views` - 

	from django.http import Http404

	def detail(request, poll_id):
		try:
    		poll = Poll.objects.get(pk=poll_id)
		except Poll.DoesNotExist:
    		raise Http404
		return render(request, 'polls/detail.html', {'poll': poll})	

 We will see the template for this view later, but the following content can be added to the 'polls / details.html` file - 
 
	{{poll}}
 
 
 