<!-- 
.. link: 
.. description: 
.. tags: Code
.. date: 2014/02/23 17:01:52
.. title: Miguel's flask mega tutorial
.. slug: miguels-flask-mega-tutorial
-->

###Miguel's flask mega tutorial

[Link](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)


####Notes

* flask:
	* Use `url_for` to build and reference URLs
	* `app.before_request`, `app.after_request` (`after_request` can be used to measure database query performance)
	* Rotating file handler
	* Don't use `window.history.back` in forms
	* Move logic away from `views`
	* Better return query objects than query results
	* Pagination (`yield_per` in sqlalchemy)
	* Unit test: 
		* use a different test database
		* regression testing: write a test for every new bug
	* Debugging: `pdb.set_trace()`
* Jinja:
	* Global variables/utilities like `config`, `g`, `session`, `url_for` are available in Jinja templates
	* `render_template` can be used in rendering HTML email templates
* Useful plugins: `coverage`, `cProfile`, werkzeug profiler


####Code snippets

* pass variable in URL:
	
	```python
	@app.route('/index/<int:page>')
	def turnto(page):
	```

* use `include` in control statements in Jinja:

	```
	{% for post in posts %}
	    {% include "post.html" %}
	{% endfor %}
	```

* rollback when error occurred:

	```python
	@app.errorhandler(500)
	def err500():
		dbsession.rollback()
	```

* Python's fake mail server: `python -m smtpd -n -c DebuggingServer localhost:1025`
* Asynchoronous decorator:

	```python
	from threading import Thread
	from functools import wraps
	
	def async(f):
		@wraps(f)
		def wrapper(*args, **kwargs):
			thr = Thread(target = f, args = args, kwargs = kwargs)
			thr.start()
		return wrapper
	```

* Expose new global variables in Jinja:

	```python
	app.jinja_env.globals['momentjs'] = momentjs
	```
