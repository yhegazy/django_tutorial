# Django Beginner's Tutorial

بِسْمِ اللَّهِ الرَّحْمَنِ الرَّحِيم

I learned Django through (https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django). This is my understanding on how to build a simple django website. I am going to try to be as basic as possible regarding code in hopes in making it easier to understand. 

Table of Contents:

01. <a href="#skeleton">Building a Skeleton Project</a>
02. <a href="#models">Models</a>
03. Creating an Admin Site
04. Creating a Home Page
05. Creating Generic Views
06. Session Framework
07. User Authentication and Permissions
08. Working with Forms
09. Creating Flatpages

<br></br><br></br>

<div id="skeleton">
  <h2>Building a Skeleton Project</h2>
  <h5>Side Note::</h5>
  <p>In this lesson, we're going to learn how to build a skeleton website for any project. This can be expanded out to other types of platforms with the technology and coding used in this lesson. The documentation does not include how to configure the web development server [webdev]. I may write this some other time.</p>
  <p>This is written in mind for Linux. For windows, replace <b>python3</b> with <b>python</b></p>
  
  <p>A website may consist of one or more sections (mainsite, blog, wiki, download, sandbox, etc). Django encourages to develop these components as separate applications, which could then be re-used in different projects if desired.</p>
  
<p>Creating any project starts with this command:</p>

    django-admin startproject projname
<p>This will create the following:</p>

    projname/
        manage.py
        projname/
            __init__.py
            settings.py
            urls.py
            wsgi.py

* manage.py | command line utility that lets you interact w/this project in various ways 
* \__init__\.py | An empty file that tells Python that this directory should be considered a Python Package 
* setting.py | settings/configuration for this project 
* url.py | The URL declaration for this project; a "table of contents" of your Django-powered site 
* wsgi.py | An entry-point for WSGI compatible web server to server your projects This tool creates a new folder and populates it with files for the different parts of the application

<p>Next, run the command</p>
    <code> python3 manage.py startapp appname</code>
    
<p>The updated project directory should look like this:</p>

      projname/
          manage.py
          projname/
          appname/
              migrations/
              __init__.py
              admin.py
              apps.py
              models.py
              tests.py
              views.py


* Migration folder is used to store "migration". These are files that allow you to automatically update your DB as you modify your models.
* __init__.py is an empty file created so that Django/Python will recognize it as a Python Package & all you to use its object within other parts of the project.
* Views should be stored in views.py, 
* Model in models.py, 
* tests in tests.py, 
* admin site configuration in admin.py 
* application registration in apps.py 

<h2>Registering the appname application</h2>
<p>Applications are registered by adding them to the INSTALLED_APPS list in the project settings. Edit: <b>projname/projname/settings.py</b> and find the definition for the <code>INSTALLED_APPS</code> list and add a new line @ the end of the list:</p>

```python
    INSTALLED_APP = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'appname.apps.AppnameConfig',
    ]
```

<h5>Side Note::</h5>
<p>The 2nd appname after </b>.apps.</b> needs to start with a capital letter.</p>
 <code>
  appname.apps.AppnameConfig
  </code>    

<p>The new line specifies the application configuration object <b>appnameConfig</b> that was generated in <b>projname/appname/apps.py</b> during the application creation.</p>

<h2>Specifying the database & other project settings</h2>

<p>It makes sense to use the same database for development & production in order to avoid minor differences in behavior. Databases are configured in <b>projname/projname/settings.py</b>.</p>

* Settings.py is used for configuring a number of other settings such as TIME_ZONE
* SECRET_KEY is used as part of Django's website security strategy
* DEBUG enables debugging log to be displayed on error rather than HTTP status code responses. Setting it to FALSE disables it.

<h2>Hooking up the URL mapper</h2>

<p>Websites created are done with URL mapper file (urls.py). They are managed through the URL patterns variable, which is a Python list of URLs. Each URL() function either associates a URL pattern to a specific view or with another list of URL pattern testing code. </p>
  
<p>Edit: <b>projname/projname/urls.py</b></p>

  ```python
  from django.conf.urls import include

  urlpatterns += [
    url(r'^appname/', include('appname.urls')),
  ]
  ```
<p>Next, reroute the root URL (127.0.0.1) to the appname url (127.0.0.1/appname). This takes on a special view function (RedirectView), which takes as its 1st argument the new relative URL to redirect to (/appname).</p>

```python
    From django.views.generic import RedirectView

    urlpatterns +=[
        url(r'^$', RedirectView.as_view(url='/appname', permanent=True)),
    ]
```

<p>Django does not serve static files like CSS, JavaScript and images by default, but can be useful for webdev. To enable:</p>

```python
    from django.conf import settings
    from django.conf.urls.static import static

    urlpatterns +=static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

<h5>Side Note::</h5>
<p>This is separate from urlpattern = [admin]. Notice the +=. This demonstrates a separation of old & new code.There are a number of ways to extend the urlpatterns list. Another way:</p>

```python
    from django.conf.urls import url, include
    from django.contrib import admin
    from django.views.generic import RedirectView
    from django.conf import settings
    from django.conf.urls.static import static
 
    urlpatterns = [
        url(r'^admin/', admin.site.urls),
        url(r'^appname/', include('appname.urls')),
        url(r'^$', RedirectView.as_view(url='/appname', permanent=True)),
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

```

<p>Finally, create a file inside your appname folder called urls.py and amend the following</p>

```python
    from django.conf.urls import url
    from . import views
    
    urlpatterns = [
    ]

```

<p>At this point of the lesson, we have completed the skeleton project. The website doesn't do anything yet, but it is worth testing it.
Run a database migration. This updates our dB to include any models in our installed app. (Anytime the model updates, these lines need to run)</p>

<code>
python3 manage.py makemigrations 
</code><br>
<code>
python3 manage.py migrate
</code>

* makemigration creates, but does not apply the migration for all apps installed in the project
* migrate applies the migration to the dB.

To run the devweb:
  <code> python3 manage.py runserver</code>
  
This concludes Building Skeleton Project
</div>

<br></br><br></br>
<div id="models">
  <h2>Models</h2> 
  <p>Django web application access and manage data through Python object referred to as models. Models define the structure of stored data.</p>
  <h5>Designing the Projname models</h5>
  <p> It is best to have separate models for every "object". An object is a group of related information. Books, Book Instances, and Authors are examples of what objects can be.</p>
  
  <p>You might want to use models to represent a options in a drop down rather than hard coding the selection choices on the web page itself. Once model and fields have been decided, you may awnt to think about relationships next. There are many types of relationships Django has to offer such as OneToOneField (one to one), ForeignKey (one to many), and ManyToManyField (many to many).</p>
  
<h5>Model primer</H5>
<p>Models are usually defined in an application's <b>models.py</b> file. They are implemented as subclasses of <code>django.db.models.Model</code> and can include fields, methods, and metadata.</p>

<h6>Fields</h6>
<p>A model can have arbitrary number of fields of any type - each one represents a column of data that we want to store in one of our db tables. Each db record (row) will consist of one of each field value. Example:</p>

    my_field_name = models.CharField(max_length=20, help_text="Enter Field Documentation.")
    

<p>The example above has a single field called <b>my_field_name</b> of type <code>models.CharField</code>. This means the field will contain strings of alphanumeric characters.</p>
  
<p>Field types are assigned using specific classes, which determine the type of record that is used to share the data in the db, along with validation criteria to be used when values are received from an HTML Form (valid value). The field type can also take arguments that further specify how the field is stored or can be used.</p>

...


</div>
