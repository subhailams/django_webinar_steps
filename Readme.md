Please Follow Below Steps

1. [Download Python](https://www.python.org/downloads/)

2. [Download Git](https://git-scm.com/downloads)

Step 1 : Setup Environment
```
pip install virtualenv
```
```
virtualenv denv
```
```
denv\Scripts\activate
```

```
pip install django
```

Step 2: Start django project
```
django-admin startproject django_todo
```
```
cd django_todo
```
Step 3 : Create todo app inside django_todo
```
python manage.py startapp todo
```

Step 4 : Update todo application

Add "todo" in INSTALLED_APPS in settings.py
```
python manage.py migrate
```

```
python manage.py createsuperuser
```

```
python manage.py runserver
```


Clone the starter files (contains starter front end files)
```
git clone https://github.com/subhailams/django_todo_app_starter.git
```

Copy "static" and "templates" folders django_todo_app_starter from to our "django_todo/todo" app

Go to  "django_todo/todo/views.py" and add below

```
from django.shortcuts import render

def index(request):
	context = {}
	return render(request, 'todo/index.html', context)

```
Add below in urlpatterns in "django_todo/urls.py"

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('todo.urls'))
]
```

Create new file "django_todo/todo/urls.py"

```
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```


