---
title: "1 - Bootstrapping a new Django Rest Framework project"
seoTitle: "Nigerian Schools API Project"
datePublished: Thu Jul 06 2023 08:31:39 GMT+0000 (Coordinated Universal Time)
cuid: cljqw2lfo07916xnvgrkn8sx0
slug: bootstrapping-a-new-django-rest-framework-project
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688557997719/aab4ecf8-2c1e-40bd-a5ed-afdf86370210.png
tags: programming-blogs, css, programming, python, web-development

---

## Introduction

I know right? The cover image is so cliche given the name of the Python framework we are going to use, but I could not resist the urge, I apologize.  
So now we will create a new Django rest framework project together with a few changes to the default settings that Django provides for us and maybe add one or two packages to the mix just for fun.

## Create environmental variables and install necessary libraries & packages

We are going, to begin with, the most basic step in creating a new project and that is to create an environment variable for this particular project. Most times the main reason for doing this is so that we can lock in the version of the packages we are using and make sure that anyone that wants to try their hands on this same project is using the same version of every package we are making use of to avoid errors.

We will be making use of Windows OS and also assume you are using any editor of your choice (I am going with everybody's favourite) and also have Python installed on your machine. If you haven't done that you can check out the [Python](https://www.python.org/downloads/) page on steps to take to get that done and also this awesome [YouTube video](https://www.youtube.com/watch?v=5agh7ucYKB4&t=110s) for more support if needed

In your terminal, type the code below

```python
py -m venv venv
```

This will create a simple environment variable called `venv` and will allow us to install all the packages that we need to get started. Now we activate this environment to begin

```python
.\venv\Scripts\activate
```

This will activate the environment variable and from there, we create our brand new Django project 🚀

Run the below code in your code editor terminal

```python
pip install django djangorestframework django-filter
```

## Django project

We have successfully set ourselves up for success and will start our project immediately.

We will create a new Django project with the code below.

```python
django-admin startproject <project name> .
```

This will create a new project for us in the current directory since that is where we have our environment variable set up.

You will need to insert a name for this project here `<project name>` and we are good to go. For now, we will use `nigerianSchools` as our project name

## Creating a custom user

From experience and also from the several errors I have had over the years, It is always good to create a custom user and extend the default Django user before making your first migration. There is a complex explanation to this but I will not bore you with all that detail what matters is for you to remember to perform this vital step before making the **first** migration in your Django project.

But if you choose to know more about this, check out this [short article](https://docs.djangoproject.com/en/4.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project) by Django on the matter.

As usual, we will run the code below in your terminal to create a new custom user app in the project directory

```python
py .\manage.py startapp custom_user
```

We should have a folder that has files similar to the ones below screenshot

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688560747917/7eb9c996-ec4f-441b-af7a-b5e047ac9379.png align="center")

So let's take this step by step and create a basic custom user. There are different other ways to extend the default user and perform this task but for us, this will do for now, we can always make changes later on to suit our needs.

### Add the new app to installed\_apps in settings.py

Next, we will need to add the new app to the list of `INSTALLED_APPS` in the `settings.py` file

```python
INSTALL_APPS = [
...
"custom_user",
...
]
```

### Custom user model

We will now create our custom user model following the code snippet below

```python
from django.db import models
from django.contrib.auth.models import AbstractUser

CONTRIBUTOR_TYPE = [
    ("Code", "Code"),
    ("Design", "Design"),
    ("Content", "Content"),
    ("Other", "Other"),
]

class CustomUser(AbstractUser):
    phone = models.CharField(max_length=20, blank=True, null=True)
    image = models.ImageField(upload_to='profile/', blank=True, null=True)
    bio = models.TextField(blank=True, null=True)
    contributor_type = models.CharField(max_length=20, default='Content', 
                        choices=CONTRIBUTOR_TYPE, blank=True, null=True)
    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['username', 'first_name', 'last_name']

    def __str__(self):
        return self.username
    
    class Meta:
        ordering = ['-last_login']
```

A brief explanation of what we just did

* import the necessary helper modules from Django that will be needed in creating this custom model.
    
* Given that we will have a select input later on in the profile section of the web application for users to indicate how they want to contribute to the project, we created a list of options that the backend should accept. And that is exactly what we did when we created `CONTRIBUTOR_TYPE` variable.
    
* Up next, we went ahead to create the custom user model while extending the `AbstractUser` is already available to us from Django, so technically we only need to add the extra fields we need to the new user model
    
* We added the fields we need for now, because of this custom model we can now make changes to the user model easily anytime we want later on the project. We can add more fields or remove fields without having any error
    
* Django enforces username/password as the default form of authentication in their login logic but we changed that to email/password when we set `USERNAME_FIELD` to email.
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">The <code>__str__</code> method in a model is used to set the interactive name of the model. For example, when we add this user as a foreign key in another model, we will see the username as the string to show us which user we are choosing rather than something like <code>user:object(1)</code></div>
</div><div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">The <code>Meta</code> class is used to set/change some other features in a model. It is not compulsory to do this for all your models, but this can help you customize some helper features to suit your needs. For more information on this check out <a target="_blank" rel="noopener noreferrer nofollow" href="https://docs.djangoproject.com/en/4.2/ref/models/options/" style="pointer-events: none">Django docs</a></div>
</div><div data-node-type="callout">
<div data-node-type="callout-emoji">ℹ</div>
<div data-node-type="callout-text">The option to add <code>blank=true &amp; null=true</code> in all my fields is also not necessary. we are only doing this to avoid having to set default in some of the fields. More information can always be found in <a target="_blank" rel="noopener noreferrer nofollow" href="https://docs.djangoproject.com/en/4.2/ref/models/fields/" style="pointer-events: none">Django's official docs</a></div>
</div>

That was a mouthful. So moving on 🪐

### Register the custom user model

We will like all our apps to show up in the admin panel provided to us by Django and so for that to happen we will need to register each app in the `admin.py` the file we have in each app folder 🛵

Registration apps are very straightforward but for this particular model, we would like to be a bit extra to see the possible options available to us from Django

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import CustomUser

fields = list(UserAdmin.fieldsets)
fields[1] = ("Personal info", {"fields": ("first_name", "last_name", 
            "email", "phone", "image", "bio", "contributor_type")})

UserAdmin.fieldsets = tuple(fields)

admin.site.register(CustomUser, UserAdmin)
```

Let's run through the code once again to see what we are up to 🤠

* Import our fresh custom user model and also a few modules from the Django library
    
* we create `fields` a variable that will house all the fields in the default user model
    
* all we did next is to update the section of the fieldset called `personal info` with the new fields we added in our custom model and also list out the ones provided to us by Django
    
* finally, we updated the `UserAdmin` fieldsets and registered the custom user to the project
    

### Update the Django settings

In the `settings.py` file of your project, locate the `AUTH_USER_MODEL` setting and updating it to point to your custom user model or create one if it is not there:

```python
AUTH_USER_MODEL = 'custom_user.CustomUser'
```

### Create database migration

Run the following commands in your terminal to generate the necessary first database migrations for your project:

```python
py .\manage.py makemigrations
py .\manage.py migrate
```

🎉 And we are done, enjoy the flexibility and control that comes with a personalized user model! 🎉

### Create a super user

To view what we have done in the admin interface we will create a super user to be able to access the panel.

```python
py .\manage.py createsuperuser
```

You will be met with the below prompts, just put in any detail you will remember

```python
Email:
Username:
First name:
Last name:
Password:
Password (again):
```

now we only need to start our development server and go to `http://localhost:8000/admin` and log in with the new credentials created above

```python
py .\manage.py runserver
```

You should see a page that looks like this

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688567986352/a17a5918-f3ee-4638-8348-df1eb45260e1.png align="center")

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">There is a tiny red circle next to the form title, it is the new theme toggle, Django has graciously added it for us so we can switch between dark and light modes. Sweet!! 🤤</div>
</div>

When you log in with the credentials you created you should see the below screenshot.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688568246220/9b84de77-0bfd-4f49-a948-23e92202bcdb.png align="center")

.

And you can also see from the below screenshot that our custom field is the right section of the user form which will allow us to capture more or less information that we will need from every user that registers on our platform. The default value we specified is also showing the contributor\_type field as expected

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688568415170/2c3d5ca8-b3fa-4d2b-b6a4-7dc51b8249f1.png align="center")

That's it!!! 🌠☄💪🏽💪🏽

Let me know what you think about the process and we will continue with the next step in the project soon  

Stay Safe