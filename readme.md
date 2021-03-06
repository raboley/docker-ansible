# Setup guide

This is meant to be a guide to follow the same steps to setup a fully automated CICD pipleline using docker and ansible to get things into the cloud. The steps will assume using a Mac.

## Initial setup

1. Install and update Brew

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

1. Install Docker

``` bash
brew install docker-compose
brew install docker
brew install docker-machine
```

install python (2.7)

``` bash
brew install python
```

install ansible 2.0 or later

``` bash
pip install ansible --upgrade
```

install packages needed for ansible modules that will work to communicate with AWS

``` bash
pip install boto boto3
```

install aws command line tools

``` bash
pip install awscli
```

installing other tools

## creating a docker virtual machine

?? do I need to do this still?
docker-machine create --driver

## Setting up the sample application

install django, the web framework for python

``` bash
pip install django==1.9
```

next create a new django project

``` bash
django-admin startproject todobackend
```

### Python virtual environments

They are a best practice for python, and allow you to have multiple versions of packages installed on the same machine. First install the virtual environemnt package

``` bash
pip install virtualenv
```

then create a virtual environment, in a folder called venv

``` bash
virtualenv venv
```

Normally the virtual environment would be outside of source control, but this is inside, but ignored for convenience.

now activate the virtual environment:

``` bash
source venv/bin/activate
```

now install all 3rd party packages using pip.

``` bash
pip install pip --upgrade
pip install django==1.9
pip install djangorestframework==3.3
pip install django-cors-headers==1.1
```

add an app in django that will be our todo app in the src folder.

``` bash
cd src
python manage.py startapp todo
```

1. then add the new project to the settings.py file in our todobackend folder.
1. update the middle ware to include cors (order is important)
1. allow all request to come through CORS (not recommended for production)

### Creating Models

Models are like classes for python. The model created here is a simple one called TodoItem with 4 fields.

``` python
class TodoItem(models.Model):
    title = models.CharField(max_length=256, null=True, blank=True)
    completed = models.BooleanField(blank=True, default=False)
    url = models.CharField(max_length=256, null=True, blank=True)
    order = models.IntegerField(null=True, blank=True)
```

Next we can create the DB schema now that we have a model. (src)

``` bash
python manage.py makemigrations todo
```

this creates the migrations folder. as the model changes, you can create incremental changes by just re-running the makemigrations command.

now to apply those changes run the migrate command (make sure you are in the venv virtual environemnt)

``` bash
python manage.py migrate
```

this will show some db stuff, and create a db.sqlite3 folder in the src folder. This hosts the model database for the application.

### Serializers

We created the model and database, but now we have to create a serialzer to tell how it should deserialize and serialize objects between our web commands.

the standard way to do this is to create serializers in a file called serializers.py, we will do that in the todo folder.

### Views

Views are the http requests that the view makes to populate itself. These are added in the view.py file. We will add a view to create the todo items in the DB, as well as a command that will delete all the todo items easily.

### Routing

Routing is using regular expression to send people to specific pages based on the url. The video says to add

```py
url(r'^', include('todo.urls')),
```

but it seems like the include function may be depricated or something, since the admin path doesn't have it in my version, but does in the video version. 

I figured out that include is a thing that can be added from some package, so I added it to the admin and non-admin routes.

The real logic is setup in the urls.py file that is in the todo folder. that creates all the actual logic for what to do when you hit a route.

### Running the application

to run the application use the manage runserver command

```bash
python manage.py runserver
```

it should give the server url, so then browse to the url. I found a bug! Misspelled Args in the view file, updated that and it works now!