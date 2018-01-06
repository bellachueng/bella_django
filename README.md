# bella_django



Prerequisites
-----------------

-	Install Python

Get the latest version of Python at https://www.python.org/downloads/ or with your operating system’s package manager.

You can verify that Python is installed by typing python from your shell; you should see something like:

	Python 3.4.x
	[GCC 4.x] on linux
	Type "help", "copyright", "credits" or "license" for more information.
	>>>



-	Verifying

To verify that Django can be seen by Python, type python from your shell. Then at the Python prompt, try to import Django:

	>>> import django
	>>> print(django.get_version())
	2.0






-	Install the Components from the Ubuntu Repositories


We will install pip( Python package manager):
The following apt commands will get you the packages you need:

	sudo apt-get update
	sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib





-	Create a Database and Database User

PostgreSQL administrative user( postgres )

	sudo su - postgres
>psql

	CREATE DATABASE myproject;

	CREATE USER myprojectuser WITH PASSWORD 'password';


We are setting the default encoding to UTF-8, which Django expects. 


	ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
	ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
	ALTER ROLE myprojectuser SET timezone TO 'UTC';

Now, all we need to do is give our database user access rights to the database we created:

	GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;

Exit the SQL prompt to get back to the postgres user's shell session:

	\q & exit 

	
-	Install Django within a Virtual Environment


You can get the virtualenv package that allows you to create these environments by typing:

	sudo pip install virtualenv

-	Make a directory to hold your Django project. Move into the directory afterwards:

	mkdir ~/myproject
	cd ~/myproject



-	Create a virtual environment to store our Django project's Python requirements by typing:

	virtualenv myprojectenv

Before we install applications within the virtual environment, we need to activate it. You can do so by typing:

	source myprojectenv/bin/activate


	
-	Install Django with pip. We will also install the psycopg2 package that will allow us to use the database we configured:

	pip install django psycopg2	
	python -m django --version
	
-	Add the dot at the end of the command so that this is set up correctly:

	django-admin.py startproject myproject .	
	


	
	
	
	
-	Configure the Django Database Settings	
	
Open the main Django project settings file located within the child project directory:

	nano ~/myproject/myproject/settings.py


If you wish to use another database, install the appropriate database bindings and change the following keys in the DATABASES 'default' item to match your database connection settings:

* ENGINE – Either 'django.db.backends.sqlite3', 
				'django.db.backends.postgresql', 
				'django.db.backends.mysql', 
				or 'django.db.backends.oracle'. 

Other backends are also available.

* NAME – The name of your database. If you’re using SQLite, the database will be a file on your computer; in that case, NAME should be the full absolute path, including filename, of that file. The default value, os.path.join(BASE_DIR, 'db.sqlite3'), will store the file in your project directory.


If you are not using SQLite as your database, additional settings such as USER, PASSWORD, and HOST must be added. For more details, see the reference documentation for DATABASES.


	DATABASES = {
		'default': {
			'ENGINE': 'django.db.backends.postgresql_psycopg2',
			'NAME': 'myproject',
			'USER': 'myprojectuser',
			'PASSWORD': 'password',
			'HOST': 'localhost',
			'PORT': '',
		}
	}	
	




-	Migrate the Database and Test your Project

	cd ~/myproject
	python manage.py makemigrations
	python manage.py migrate

	
-	After creating the database structure, we can create an administrative account by typing:

	python manage.py createsuperuser



-	Starting up the Django development server:

	python manage.py runserver 0.0.0.0:8000

	

-	Visit your server's domain name or IP address followed by :8000 to reach default Django root page:

	http://server_domain_or_IP:8000


-	Creating the Polls app
	
	Notice:
	Projects vs. apps

What’s the difference between a project and an app? An app is a Web application that does something – e.g., a Weblog system, a database of public records or a simple poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.
	
	python manage.py startapp polls
That’ll create a directory polls, which is laid out like this:

	polls/
		__init__.py
		admin.py
		apps.py
		migrations/
			__init__.py
		models.py
		tests.py
		views.py
		
	
-	Write your first view	
	
* polls/views.py
	
	from django.http import HttpResponse


	def index(request):
		return HttpResponse("Hello, world. You're at the polls index.")
	
	
	
* polls/urls.py

	from django.urls import path

	from . import views

	urlpatterns = [
		path('', views.index, name='index'),
	]
	
	
* myproject/urls.py
	from django.urls import include, path
	from django.contrib import admin

	urlpatterns = [
		path('polls/', include('polls.urls')),
		path('admin/', admin.site.urls),
	]
	

	
	
	
The path() function is passed four arguments, two required: route and view, and two optional: kwargs, and name. At this point, it’s worth reviewing what these arguments are for.
	
path() argument: route	
path() argument: view	
path() argument: kwargs	
path() argument: name	
	
	
	
	
-	Creating models	
	
	
	
* polls/models.py
	from django.db import models


	class Question(models.Model):
		question_text = models.CharField(max_length=200)
		pub_date = models.DateTimeField('date published')


	class Choice(models.Model):
		question = models.ForeignKey(Question, on_delete=models.CASCADE)
		choice_text = models.CharField(max_length=200)
		votes = models.IntegerField(default=0)	
	
	
	
-	Activating models	
	
	
* mysite/settings.py

	INSTALLED_APPS = [
		'polls.apps.PollsConfig',
		'django.contrib.admin',
		'django.contrib.auth',
		'django.contrib.contenttypes',
		'django.contrib.sessions',
		'django.contrib.messages',
		'django.contrib.staticfiles',
	]
	
	
	
-	Django knows to include the polls app:

	python manage.py makemigrations polls	
	
	
	
You should see something similar to the following:

	Migrations for 'polls':
	  polls/migrations/0001_initial.py:
		- Create model Choice
		- Create model Question
		- Add field question to choice	
	
	
	
	
	
	
	
	
	

FILE ORGANIZATION
-----------------

This project contains the following directories:

* source


* guidelines / templates



	Let’s look at what startproject created:

	myproject/
		manage.py
		myproject/
			__init__.py
			settings.py
			urls.py
			wsgi.py


			
These files are:

* The outer myproject/ root directory is just a container for your project. Its name doesn’t matter to Django; you can rename it to anything you like.

* manage.py: A command-line utility that lets you interact with this Django project in various ways. You can read all the details about manage.py in django-admin and manage.py.

* The inner myproject/ directory is the actual Python package for your project. Its name is the Python package name you’ll need to use to import anything inside it (e.g. myproject.urls).

* myproject/__init__.py: An empty file that tells Python that this directory should be considered a Python package. If you’re a Python beginner, read more about packages in the official Python docs.

* myproject/settings.py: Settings/configuration for this Django project. Django settings will tell you all about how settings work.

* myproject/urls.py: The URL declarations for this Django project; a “table of contents” of your Django-powered site. You can read more about URLs in URL dispatcher.

* myproject/wsgi.py: An entry-point for WSGI-compatible web servers to serve your project. See How to deploy with WSGI for more details.





To run the scripts:
- 


On a Linux machine, you can use one of these commands to install all the tools:


On a Mac:

  
  