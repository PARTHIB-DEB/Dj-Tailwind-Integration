![Dj-Tailwind](Dj-Tailwind.jpeg)

# Integrating Django with Tailwind (Without Using any Packadge) Guide

 🚀 This repository consists about How you can use TailwindCSS framework within Django Server.
 
 🤔 Yeah !! I know there are some packadges on this topic. But they also do the same steps like how I have done.
  But they lack proper changes in CSS-reloading (What I have faced 🙂)
 
 🐎 That's why , without using those packadges , Let's manually create the environment. Believe me 🌝, that will be a more persistent alternative.

 🔥 Although There are two more ways - 1) Using PlayCDN or 2) Connecting Tailwind Server Via REST API (because here the frontend is part of another project)
 
 💡 Let's see How we can build this configuration properly .

## **❗❗This Technique (especially due to the effect of Django-Compressor) , may hamper your django-admin pannel's Styling**

 # Requirements :
 🔔 Django (Any Version , Recommended >=3.5)
 
 🔔 Django-Compressor (For Converting the Tailwind CSS into Vanilla CSS )
 
 🔔 Django-Browser-Reload (To Reload the all static (CSS , JS) changes in screen)
 
 🔔 Tailwind Css (Any Version)
 
 🔔 Any Database (For Newbiees , Prefer either Sqlite or Postgres )
 

 # Setup :
 ## 1. Install Virtualenv and Requirements.txt :
 ```bash
  python -m venv venv

  .\venv\Scripts\activate

 ```
 ## 2. Django Server setup
 In the beginning , let's create the Django Server. Run the Following Commands to set the environment.
 
 - Install Django
 ```bash
  python -m pip install django
 ```
 - Create a Project (You can create an APP also, but for newbiees I prefer , just create the project and make necessary changes in it)
 ```bash
  django-admin startproject myapp
 ```
 - Install Django-compressor and Django-browser-reload
 ```bash
  python -m pip install django-compressor django-browser-reload
 ```

 ## 3. Install Tailwindcss (Along with Postcss and Vite)
 ``` bash
  npm init -y
  npm install -D tailwindcss postcss autoprefixer vite
  npx tailwindcss init -p
 ```

## 4. Configuring project architechture in myapp
Now let's configure the project. Before that Create two directories - **templates** , **static** in that directory where **manage.py** exists.
- **Settings.py**
```bash
  INSTALLED_APPS = [
    ...
    'compressor', #new
    'django_browser_reload' #new
]

MIDDLEWARE = [
    ...
    "django_browser_reload.middleware.BrowserReloadMiddleware", # new
]

TEMPLATES = [
    {
        ...
        'DIRS': [BASE_DIR / 'templates'], # new
        ...
    }
    ...
]

COMPRESS_ROOT = BASE_DIR / 'static' # new

COMPRESS_ENABLED = True # new

STATICFILES_FINDERS = ('compressor.finders.CompressorFinder',) # new
```
- **urls.py**
```bash
  from django.urls import path , include
  from .views import index

  urlpatterns = [
      ...
      path('',index,name='index'),
      path("__reload__/", include("django_browser_reload.urls"))
  ]
```
- **views.py** (Create this file , this file automatically comes with django-APPS)
```bash
  from django.shortcuts import render
  
  def index(request):
  	return render(request, 'index.html')
```
- _templates_ : Folder for storing .html files
- _static_ : For storing .css files , create two file - input.css , output.css (this css file will contain the compressed version of tailwindcss)

## 5. Configuring Tailwind Portions
Now Let's configure some files that came after the installation of tailwindcss
- **tailwind.config.js**
  
```bash
  module.exports = {
  ...
  content: ['./templates/**/*.html'],
  ...
}
```
- **Add this lines in _input.css_**
```bash
   @tailwind base;
   @tailwind components;
   @tailwind utilities;
```
- **Now run two commands (every time when you do any changes in css using tailwind)**
```bash
   npx tailwindcss -i ./static/input.css -o ./static/output.css

   python manage.py runserver
```
- **Watch this screenshot and check once with your project structure**


![Screenshot (870)](https://github.com/PARTHIB-DEB/Test-Dj-Tailwind/assets/103876861/1ca3a8c5-3086-4d97-a88f-6d429201f1e1)
  
## 6. HTML file codes
- _base.html_ :
```bash
  {% load compress %}
  {% load static %}
  
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
  	<meta charset="UTF-8">
  	<meta http-equiv="X-UA-Compatible" content="IE=edge">
  	<meta name="viewport" content= "width=device-width, initial-scale=1.0">
  	<title>Django + Tailwind CSS</title>
  	{% compress css %}
  	<link rel="stylesheet" href="/static/output.css">
  	{% endcompress %}
  </head>
  
  <body class="bg-green-50">
  	<div class="container mx-auto mt-4">
  		{% block content %}
  		{% endblock content %}
  	</div>
  </body>
  
  </html>
```
- _index.html_ :
```bash

  {% extends "base.html" %}
  {% block content %}
  <h1 class="say-name font-extrabold text-center text-6xl">MY NAME IS PARTHIB</h1>
  {% endblock content %}

```
