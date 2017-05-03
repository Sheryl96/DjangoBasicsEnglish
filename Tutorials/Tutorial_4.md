#Tutorial IV

##Creating Django Form 

Those who have HTML knowledge, they are already familiar with `<form>`. We can see how we can handle form data in Django. Let's add code in `poll / detail.html` template -

	<h1> {{poll.question}} </h1>

	{% If error_message%} <p> <strong> {{error_message}} </ strong> </ p> {% endif%}

	<form action = "{% url 'poll: vote' poll.id%}" method = "post">
	{% csrf_token%}
	{% for choice in poll.choice_set.all%}
    	<input type = "radio" name = "choice" id = "choice {{forloop.counter}}" value = "{{choice.id}}" />
    	<label for = "choice {{forloop.counter}}" {{choice.choice_text}} </ label> 		<br />
	{% endfor%}
	<input type = "submit" value = "vote" />
	</form>
	
Here - 

* We set a radio button for each option 'choice'. The user submits the form by selecting any option, 
* We have used `action` of the form as `{% url 'poll: vote' poll.id%} `and` post` as `method`. `Url` is a template tag which outputs the correct url using the value` polls: vote` and `poll.id`.
* `forloop.counter` indicates how many times the for tag has gone through its loop 
* Django naturally tries to stop Cross Site Request Forgery. For this, it is essential to have `{% csrf_token%} 'in each form. Although it can be disabled, but it should be used.

We created the following URLconfig in the previous chapter - 

	url(r'^(?P<poll_id>\d+)/vote/$', views.vote, name='vote'), 

Add the following to `polls/views.py`:
 	
	from django.shortcuts import get_object_or_404, render
	from django.http import HttpResponseRedirect, HttpResponse
	from django.core.urlresolvers import reverse
	from polls.models import Choice, Poll
	# ...
	def vote(request, poll_id):
		p = get_object_or_404(Poll, pk=poll_id)
		try:
    		selected_choice = p.choice_set.get(pk=request.POST['choice'])
		except (KeyError, Choice.DoesNotExist):
    		# Redisplay the poll voting form.
    		return render(request, 'polls/detail.html', {
        		'poll': p,
        		'error_message': "You didn't select a choice.",
    		})
		else:
    		selected_choice.votes += 1
    		selected_choice.save()
    		# Always return an HttpResponseRedirect after successfully dealing
    		# with POST data. This prevents data from being posted twice if a
    		# user hits the Back button.
    		return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

Here -  

* `request.POST` is a Python dictionary. All the data key values ​​here are also here. For example: `request.POST ['choice']` can get the value of `choice`. If the POST data does not have `choice ', then the django  `keyError` will rival. That's why we used try ... except blocks.
* Here we are returning `HttpResponseRedirect` without sending` HttpResponse`. We can send the user to another address without showing any output through this.
* `reverse ()` function returns the full url from the name of the view as 'template% {% url%}'.

Now if anyone voted in the application, we will redirect to the results () `view`. Let's create that view.

	def results(request, poll_id):
		poll = get_object_or_404(Poll, pk=poll_id)
		return render(request, 'polls/results.html', {'poll': poll})  
  	
Now let us create the template for the view - 

	<h1>{{ poll.question }}</h1>

	<ul>
	{% for choice in poll.choice_set.all %}
		<li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|	pluralize }}</li>
	{% endfor %}
	</ul>

	<a href="{% url 'polls:detail' poll.id %}">Vote again?</a>

Run this application and see how it works. 


### Summary

If you can easily understand these 4 sections, you can easily create simple websites using Django. But it does not stop here. This tutorial has been written by changing the official tutorial for making it understandable to the young ones. Therefore, this tutorial was based on basics of django.
