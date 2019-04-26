# Django Beginner's Tutorial

بِسْمِ اللَّهِ الرَّحْمَنِ الرَّحِيم

I learned Django through (https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django). This is my understanding on how to build a simple django website. I am going to try to be as basic as possible regarding code in hopes in making it easier to understand. 

Table of Contents:

01. <a href="#skeleton">Building a Skeleton Project</a>
02. <a href="#models">Models</a>
03. <a href="#admin">Creating an Admin Site</a>
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

<p>The order that fields are declared will affect their default order if a model is rendered in a form and the admin site; though this may be overridden.</p>

<h5>Common Field Arguments</h5>

* help_text: Provides a text label for HTML forms
* verbose_name: A human-readable name for the field used in field labels. If not specified, Django will assign one.
* default: The default value for the field. This can be a value or a callable object, in which case, the object will be called every time a new record is created.
* null: If True, Django will store blank values as NULL in db. Default is False
* blank: If True, the field is allowed to be blank in the forms. Default is False, which means Django's form validation will force you to enter a value. This is often used with null = true
* choices: A group of choices for this field. Think select boxes.
* primary key: If True, sets the current field as the primary key for the model. If no field is specified, Django will add a field for this purpose.

<a href="https://docs.djangoproject.com/en/1.10/ref/models/fields/#field-options" target="_blank">Full Field Options List</a>

<h5> Common Field Types</h5>

* CharField: used to define short-to-mid sized fixed length strings. You must specify max_length of the data to be stored. 
* TextField: used for large arbitrary-length strings. You may specify a max_length for the field, but this is used only when the field is displayed in a form (not enforced by db).
* IntegerField: a field for storing integer (whole number) values and for validating entered values as integers in forms.
- DateField and DateTimeField: for storing/representing dates and date/time information. 
  - auto_new = True: Sets the field to the current DTime every time the model is saved.
  - auto_now_add: Only set the date when the model is first created.
  - default: set default date that can be overridden by the user
* EmailField: store and validate email addresses
* FileField and ImageField: upload files and images respectively.
* AutoField: special type of IntegerField that auto increment primary key of this type is automatically added to the model
* ForeignKey: used to specify one-to-many relationship. The "one" sided of the relationship is the model that contains the key.
* ManyToManyField: many-to-may relationship. Example - a book can have several genres and each genre can contain several books.

<a href="https://docs.djangoproject.com/en/1.10/ref/models/fields/#field-types" target="_blank">Full Field Types List</a>

<h5>MetaData</h5>
<p>Metadatas can be declared by the class Meta.</p>

```python
    class Meta:
        ordering = ["-my_field_name"]
```

<p>One of the most useful features of the metadata is control the default ordering to the ordering attribute. The ordering will depend on the type of field.</p>

<p>Prefixing the minus sign (-) to reverse the sorting order.</p>

```python
ordering = ["Title", "-pubdate"]
```

<p>In this example, the books would be sorted alphabetically from A - Z, then by publication date inside each title, from newest to oldest.</p>
<p><code>verbose_name</code> is a common attribute and is written: <code>verbose_name = "BetterName" </code>. This is both for its plural and singular form.</p>

<a href="https://docs.djangoproject.com/en/1.10/ref/models/options/" target="_blank">Full Model Options List</a>

<h5>Methods</h5>
<p>A model can also have methods.</p>

<p>Minimally, in every model you should define the standard Python class method <code>__str__()</code> to return a human-readable string for each object. The string represent individual records in the administration site. This will return a title or name field from the model.</p>

```python
    def __str__(self):
        return self.field_name
```

<p>Another common method to include is <code>get_absolute_url()</code>. This returns a URL for displaying individual model records on the website. A typical pattern shown:

```python
        def get_absolute_url(self):
            """
            Returns the URL to access a particular instance of the model
            """
            return reverse("model-detail-view", args=[str(self.id)])
```

<h5>Model Management</h5>
<h6>Creating & Modifying Records</h6>
<p>To create a record, you can define an instance of the model then call <code>save()</code>

```python
    #Create a new record using the model's constructor
    a_record = ModelClassName(my_field_name="Instance #1")

    #Save the object into the database
    a_record.save()

```

<p>You can access the fields in this new record using the dot syntax (like JavaScript) and change values. Call <code>save()</code> to store the modifications.</p>

```python
    #Access model field values using Python attributes
    print(a_record.id) # Should return 1 for 1st record
    print(a_record.my_field_name) # Should print "Instance #1"

    #Change record by modifying the field, then call save()
    a_record.my_field_name = "New Instance Name"
    a_record.save()

```

<h6>Searching for Records</h6>
<p>To explain clearly, the Book object will be used as a model. You can search for records that match a certain criteria using the model's object attribute. You will need to use QuerySet (<code>objects.all()</code>) to get all records for a model. The QuerySet as an iterable object.</p>

```python
    all_books = Book.objects.all()
```

<p>Django's <code>filter()</code> method allows us to filter the returned QuerySet to match a specified text and numeric field against a particular criteria. In this example, it is searching for the word <b>"wild"</b> in the title as well as counts them.
  
```python
    wild_books = Books.objects.filter(title__contains="wild")
    number_wild_books = Books.objects.filter(title__contains="wild").count()
```

<p>The field to match and the type of match are defined in the filter parameter name using the format <code>field_name__match_type</code>.</p>

<p>Other types of matching:</p>

- icontains: case insensitive
- iexact: case-insensitive exact match
- exact: case-sensitive exact match
	
<a href="https://docs.djangoproject.com/en/1.10/ref/models/querysets/#field-lookups" target="_blank"> Full Field Lookup List</a>


<p>Sometimes, you'll need to filter on a field that defines a ForeignKey relationship to another model. In this case, you can <i>"index"</i> to fields within the related model with additional double underscores. In this example, a specific genre pattern is used as a filter for a book</p>

```python
    books_containing_genre = Books.objects.filter(genre__name__icontains = 'fiction')
    #Will match on Fiction, Science fiction, non-fiction, etc.
```

<h5>Defining the Projname Model</h5>
<p>In this section, we'll start defining the models for the projname. Open <b>/projname/appname/models.py</b>. 
 
  ```python
    from django.db import models
    # Create your models here
```

<p>This model (below) can be served as a template for any <b>appname</b> model. Notice the captalization of <code>ModelClassName</code> in various parts of the code. This is important! </p>

```python

from django.db import models
from django.urls import reverse # Used to generate URLs by reversing the URL pattern
from PIL import Image

# Create your models here.

class ModelClassName(models.Model):
    """
    Model representing the ModelClassName.  
    """
    foo = models.CharField(max_length=100, help_text="Enter some radom text here.", null=True, blank=True)
    bar = models.ManyToManyField(ModelClassName1, help_text="ModelClassName1 is a another model listed in the models.py file")
    foobar = models.ForeignKey('ModelClassName2', on_delete=models.SET_NULL, null=True)
    dateVariable = models.DateTimeField(auto_now_add=True, blank=True)
        
    #FileField used because a home page can have imagery
    images = models.FileField(null=True, blank=True)
    
    def __str__(self):
        """
        String for representing the Model object (in Admin site)
        """
        return self.title

    def get_absolute_url(self):
        """
         Returns the url to access a particular item.
        """
        return reverse('modelname-list', args=[str(self.id)])

```
<p>The variable <code>bar</code> has a ManyToManyField relationship. A good way of wrapping your head around it is to think like this - A book can have multiple genres and a genre can have multiple books. </p>

<p>The variable <code>foobar</code> has a ForeignKey relationship. ForeignKey relationship can be thought as each book has 1 author, but any author may have many books. Also, take notice this is wrapped with single quotes (''). </p>

<p>Here is an example on how to build a drop down list:</p>

```python
 #Build out the drop down here:
        LOAN_STATUS = (
            ('m', 'Maintenance'),
            ('o', 'On loan'),
            ('a', 'Available'),
            ('r', 'Reserved'),
        )
                                                #assign DD here
        status = models.CharField(max_length=1, choices=LOAN_STATUS, blank=True, default='m', help_text='Book availability')
```

<p>Lastly, things to make mention: </p>

1. <code>on_delete=models.SET_NULL</code> | set the value of the author to NULL if the associated author record is deleted.
2. <code>UUIDField</code> | used for the id field to set it as the primary_key for this model. This type of field allocates a globally unique value for each instance (one for every book you can find in the library).
3. <code>DateField</code> | used for the due_back date. This value can be blank or null The model metadata Class Meta uses this field to order records when they are returned in a query.
4. <code>status</code> | a CharField that defines a choice/selection list.

<p> The model defines <code>__str__()</code>, using the book's title field to represent a Book record. The final method, <code>get_absolute_url()</code> returns a URL that can be used to access a detail record for this model.</p>

<code>python3 manage.py makemigrations</code><br>
<code>python3 manage.py migrate</code>

<p>To run the devweb:</p>
<code> python3 manage.py runserver</code>

<p>This concludes Using Models</p>
</div>
<div id="admin">
	<h2>Creating an Admin Site</h2>
	<h5>Overview</h5>
	<p>The Django admin application can use your models to automatically build a site area that you can use to create, view, update and delete records. It is useful for managing data in production, depending on the type of website. Django recommends it only for internal data management (just used by admins). All the configuration required to include the admin application in your website was done automatically at the creations of the <a href="#skeleton"> Skeleton Project</a>. As a result, all you must do to add your models to the admin application is to register them</p>

<h5>Registering models</h5>
<p>Open <b>/projname/appname/admin.py</b>. You will see a simple template</p>

```python
    from django.contrib import admin

    #Register your models here.
```

<p>To register the models, copy the text at the bottom of the file. The code imports the models and then calls <code>admin.site.register()</code> to register each of them. This is the simplest way of registering the model(s) with the site.</p>

```python
    from django.contrib import admin
    from .models import ModelClassName1, ModelClassName2, ModelClassNameX

    #Register your models here.
    admin.site.register(ModelClassName1)
    admin.site.register(ModelClassName2)
    admin.site.register(ModelClassNameX)
```
<h5>Creating a superuser</h5>
<p>In order to log into the admin site, we need a user account. In order to view and create records, we need this user to have permissions to manage all our objects.</p>

```
    python3 manage.py createsuperuser
```

<p>This command creates full access to the site and all needed permissions using <b>manage.py</b>. You will be prompted to enter a username, email address and a strong password.
   
```   
    python3 manage.py runserver
```

<h5>Logging in and using the site</h5>
<p>To login to the site, open <b>http://127.0.0.1:8000/admin</b> and enter your newly created superuser credentials. This part of the site displays all the models, grouped by installed application. You can click on a model name to go to a screen that lists all its associated records. They can be further clicked to edit them. Also, you can directly click to <b>Add</b> link next to each model to start creating a record of that type.</p>

<p>Click on the <b>Add</b> link to the right of the <b>ModelClassName</b> to create a new item. Enter values for the fields. When done, press <b>SAVE</b>, </b>Save and add another</b>, or <b>Save and continue editing</b> to save the record.</p>

<h5>Advanced configuration</h5>
<p>Django does a good job of creating a basic admin site using the information from the registered models:</p>

1. Each model has a list of individual records, identified by the string created with the model's <code>__str__()</code> method, and linked to detail views/forms for editing.
2. The model detail record forms for editing and adding records contain all the fields in the model, laid out vertically in their declaration order.

<p>You can further customize the interface to make it even easier to use. </p>

- List Views:
1. add additional fields/information displayed for each record.
2. add filters to select which records are listed.
3. add additional options to the actions menu in list views and choose where this menu is displayed on the form.

- Detail views:
1. Choose which fields to display (or exclude), along with their order, grouping whether they are editable, the widget used, orientation, etc.
2. Add related fields to a record allow inline editing

<p>You can find a complete reference of all the admin site in The <a href="https://docs.djangoproject.com/en/1.01/ref/contrib/admin/"> Django Admin site</a>.</p>

<h6>Register a ModelAdmin class</h6>
<p>The <code>ModelAdmin</code> class allows you to change how a model is displayed in the admin site. Open <code>/projname/appname/admin.py</code> and comment out the original registration for the <code>ModelClassName</code> class:</p>

```python
    #admin.site.register(ModelClassName)
```
<p>And add a new <code>ModelClassName</code> and registration as shown below:</p>

```python
    # Define the admin class
    class ModelClassNameAdmin(admin.ModelAdmin):
        pass

    # Register the admin class with the associated model
    admin.site.register(ModelClassName, ModelClassNameAdmin)

```

<p>Repeat for the other <code>ModelCassName</code> classes. You can register the model with a decorator (as shown below) instead of <code>admin.site.register()</code>:</p>

```python
    @admin.register(ModelClassName)
    class ModelClassNameAdmin(admin.ModelAdmin):
        pass
```

<h6>Optional - Configure list views</h6>
<p>To create column headers, replace the pass as shown below. Review any other classes that may require this update:</p>

```python
    @admin.register(ModelClassName)
    class ModelClassNameAdmin(admin.ModelAdmin):
        list_display = ('foo', 'bar', 'foobar')
```

<p>Finally, fields are displayed vertically by default, but will display horizontally if you further group them in a tuple, as so:</p>

```python
    @admin.register(ModelClassName)
    class ModelClassNameAdmin(admin.ModelAdmin):
        list_display = ('foo', 'bar', 'foobar')
        fields = [('foo', 'bar'), 'foobar']

```

<p>You can add "sections" to group related model information within the detail form, using the fieldset attributes. For example:</p>

```python
    @admin.register(ModelClassName)
    class ModelClassNameAdmin(admin.ModelAdmin):
        list_display = ('foo', 'bar', 'foobar')
        list_filter = ('ascending', 'descending')
    
        fieldsets = (
            (None, {
                'fields': ('test1','test2', 'id')
            }),
            ('Availability', {
                'fields': ('ascending', 'descending')
            }),
        )
```

<p>To declare inline editing of associated records:</p>

```python
    class ModelClassNameInline(admin.TabularInline):
        model = ModelClassName

    @admin.register(ModelClassName)
    class ModelClassNameAdmin(admin.ModelAdmin):
        list_display = ('foo', 'bar', 'foobar_tag')
        inlines = [ModelClassNameInline]
```

<p>Restart the application to view your changes.<br>
This concludes Creating an Admin site</p>


...







</div>
