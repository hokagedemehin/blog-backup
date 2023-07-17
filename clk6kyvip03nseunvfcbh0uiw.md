---
title: "3 - Changing Database from SQLite to Postgres"
datePublished: Mon Jul 17 2023 08:05:09 GMT+0000 (Coordinated Universal Time)
cuid: clk6kyvip03nseunvfcbh0uiw
slug: 3-changing-database-from-sqlite-to-postgres
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1689159176336/d083d9fb-789a-4d1c-959e-0f93c16d00db.png
tags: programming-blogs, programming, python, web-development, django-rest-framework

---

It is always a good practice to start a Django project with Postgres for apparent reasons. Sometimes the default database shipped with Django may suffice for you. However, as the project grows and more likely than not if you have to collaborate on any project you are working on, it becomes challenging to be in sync when the database is only visible on your computer.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689160856962/ff62468c-6c40-4478-befa-909c4c4bbba7.png align="center")

😂😂😂😂

Anyway, let's begin this process and get the ball rolling. By now we should have a `db.sqlite3` file in our route directory. This was due to our migration, which created the database locally on our machine.

### Create a Postgres database on Railway

We are going to make use of a free service available to us from Railway to create this database. All you need to do is create an account with them and follow the steps below

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689161198212/d798ff04-93d4-43b9-985e-f9fffaa7e4c5.png align="center")

Click on the `start a new project` button to begin

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689161288974/8f09dbb5-11df-48ec-b279-344e011683fb.png align="center")

then select the third option which is `Provisional Postgres` to create the database

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689161355899/411ff6e5-d749-40b5-aa95-5aaba6f879f1.png align="center")

And that is all, the project is done and ready for use, but we are not entirely done yet. We need to connect this database to our project locally so that when we run our server everything will work as intended.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689161552973/33d78c05-faa7-47ea-b083-0605d38b7346.png align="center")

As you can see the project is empty but that will be filled with data very soon, for now, we need to go to the `connect` tab to get the necessary credentials to connect with this database.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689162531357/1e9a0041-440e-4630-8fdf-3a8d90b465ac.png align="center")

Now to the good stuff 🍪😎

In the Connect tab, we can see in the `available variable` section there are six variables created for us to use to link our project to the database. To do this right, I will show you how to use environment variables to store sensitive information and how you can extract them from your code

### Install [Django-environs](https://django-environ.readthedocs.io/en/latest/index.html) package

We are now going to set up our project to be able to use environment variables in a way that will allow us to secure our private keys and passwords and use them in our code.

Following the instruction on the [installation](https://django-environ.readthedocs.io/en/latest/install.html) page, all we have to do is run `python -m pip install django-environ` and add create a file in your root directory called `.env` (if you haven't done so). In this file add all the variables from Railway the way it appears in that section

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">if you hover over each variable they should be two icon buttons that will pop up, an eye icon and a copy icon. Use the eye icon to see the content of a variable and use the copy icon to copy it</div>
</div>

```xml
DATABASE_URL=<your database url>
PGDATABASE=<your database name>
PGUSER=<your database user>
PGPASSWORD=<your database password>
PGHOST=<your database host>
PGPORT=<your database port>
```

Just before we skip an important step we need to install another Python package called `psycopg2`. This is a popular PostgreSQL database adapter for the Python programming language, simple.

```xml
python -m pip install psycopg2
```

### Update Settings.py file

Now we can connect our new database to the project using the below steps

```python
settings.py

import environ
import os

env = environ.Env(
    # set casting, default value
    DEBUG=(bool, False)
)
BASE_DIR = Path(__file__).resolve().parent.parent

environ.Env.read_env(os.path.join(BASE_DIR, ".env"))

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",
        "NAME": env("PGDATABASE"),
        "USER": env("PGUSER"),
        "PASSWORD": env("PGPASSWORD"),
        "HOST": env("PGHOST"),
        "PORT": env("PGPORT"),
    }
}
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">It is also possible to adjust other parts of your settings o make use of this new feature to hide sensitive data, for example, the SECRET_KEY</div>
</div>

If we try to spin up our local server again we should see this warning on our terminal.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689165626178/a2af0e23-7a52-45d8-8646-e90b3521ed70.png align="center")

This means everything is working fine because now we are using a new database that has nothing on it. No migration has been made yet, so to correct this error we make migrations like we did previously when we created a custom user.

```python
python -m manage.py migrate
```

If you take a look at the Postgres database now, they should be some data available there due to the migration we just made

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1689165992604/d300bf1f-5671-4475-8d26-40d8b80bc5fd.png align="center")

Now just before we start our server again, we would like to create a super user to be able to login to the admin panel. Remember that this is a new database so the previous one we created will not work (You can try it out to see).

The process can be found [here](https://demibk.hashnode.dev/bootstrapping-a-new-django-rest-framework-project#heading-create-a-super-user).

Now we start our local server again and we are in business as usual 🌟⭐❇

From here we can move on to deploying our project online for public use

✌🏽✌🏽