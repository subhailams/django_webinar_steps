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
Run server 

Add {% load static %} in todo/templates/todo/index.html

```
{% load static %}
```
Update below lines in "django_todo/todo/templates/todo/index.html"

```
<link rel="stylesheet" href="{% static 'todo/bs/css/flatly.min.css' %}" />
<link rel="stylesheet" href="{% static 'todo/styles.css' %}" />
```

```
<link rel="stylesheet" href="{% static 'todo/bs/css/flatly.min.css' %}" />
<link rel="stylesheet" href="{% static 'todo/styles.css' %}" />
```
Create your models in "django_todo/todo/models.py"

```
class Todo(models.Model):
    text = models.CharField(max_length=40)
    complete = models.BooleanField(default=False)

    def __str__(self):
        return self.text
```

Register your models "django_todo/todo/admin.py"
```
from .models import Todo
admin.site.register(Todo)
```

Migrate your models using command below
```
python manage.py makemigrations
python manage.py migrate
```

Goto admin http://127.0.0.1:8000/admin/ and add dummy todo list in database

Add below in "django_todo/todo/views.py"
```
from django.shortcuts import render, redirect

from .models import Todo

def index(request):
    todo_list = Todo.objects.order_by('id')
    context = {'todo_list' : todo_list}
    return render(request, 'todo/index.html', context)

```

Update index.html with for loop
```
						{% for todo in todo_list %}
							{% if todo.complete %}
							<li class="list-group-item todo-completed">{{ todo.text }}</li>
							{% else %}
							<a href="#"><li class="list-group-item">{{ todo.text }}</li></a>
							{% endif %}
						{% endfor %}

```

Create "django_todo/todo/forms.py" for TodoForm
```
from django import forms 

class TodoForm(forms.Form):
    text = forms.CharField(max_length=40, 
        widget=forms.TextInput(
            attrs={'class' : 'form-control', 'placeholder' : 'Enter todo e.g. Delete junk files', 'aria-label' : 'Todo', 'aria-describedby' : 'add-btn'}))
```

Update below in "django_todo/todo/views.py"
```
from django.shortcuts import render, redirect
from django.views.decorators.http import require_POST

from .models import Todo
from .forms import TodoForm

def index(request):
    todo_list = Todo.objects.order_by('id')

    form = TodoForm()

    context = {'todo_list' : todo_list, 'form' : form}

    return render(request, 'todo/index.html', context)

```
Add csrf token and form.text in forms.py
```
					<form action="#" method="POST" role="form">
						{% csrf_token %}
						<div class="form-group">
							<div class="input-group">
							  {{ form.text }}
							  <span class="input-group-btn">
								  <button type="submit" class="btn btn-default" id="add-btn">ADD</button>
							  </span>
							</div>
						</div>
					</form>
```
Add below in "django_todo/todo/views.py"

```
@require_POST
def addTodo(request):
    form = TodoForm(request.POST)

    if form.is_valid():
        new_todo = Todo(text=request.POST['text'])
        new_todo.save()

    return redirect('index')
```
Add below in todo/urls.py
```
path('add', views.addTodo, name='add'),
```

Update below in index.html
```
					<form action="{% url 'add' %}" method="POST" role="form">
```

Add complete todo view in views.py

```
def completeTodo(request, todo_id):
    todo = Todo.objects.get(pk=todo_id)
    todo.complete = True
    todo.save()

    return redirect('index')
```
Add below url in todo/urls.py
```
    path('complete/<todo_id>', views.completeTodo, name='complete'),

```

Update below in index.html

```
							<a href="{% url 'complete' todo.id %}"><li class="list-group-item">{{ todo.text }}</li></a>

```
