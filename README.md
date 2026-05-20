# Django Lab Programs Collection

---

# Program 1

## Folder Structure
```txt
lab1/
│── manage.py
│
├── lab1/
│   ├── settings.py
│   ├── urls.py
│
├── p1/
│   ├── views.py
│
└── templates/
    └── home.html
```

## Create Project & App
```bash
django-admin startproject lab1
cd lab1
python manage.py startapp p1
```

---

## p1/views.py
```py
from django.shortcuts import render
import datetime

def home(request):
    current = datetime.datetime.now()

    ahead = current + datetime.timedelta(hours=4)
    before = current - datetime.timedelta(hours=4)

    return render(
        request,
        'home.html',
        {
            'current': current,
            'ahead': ahead,
            'before': before
        }
    )
```

---

## templates/home.html
```html
<h1>
    Current Time:
    {{ current }}
</h1>

<h1>
    4 Hours Ahead:
    {{ ahead }}
</h1>

<h1>
    4 Hours Before:
    {{ before }}
</h1>
```

---

## lab1/urls.py
```py
from django.contrib import admin
from django.urls import path
from p1 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home),
]
```

---

# Program 2

## Folder Structure
```txt
lab2/
│── manage.py
│
├── lab2/
│   ├── settings.py
│   ├── urls.py
│
├── p2/
│   ├── views.py
│
└── templates/
    ├── layout.html
    ├── home.html
    ├── about.html
    └── contact.html
```

## Create Project & App
```bash
django-admin startproject lab2
cd lab2
python manage.py startapp p2
```

---

## p2/views.py
```py
from django.shortcuts import render

def home(request):
    return render(request, 'home.html')

def about(request):
    return render(request, 'about.html')

def contact(request):
    return render(request, 'contact.html')
```

---

## templates/layout.html
```html
<h1>My Website</h1>

<hr>

<a href="/home/">Home</a>
<a href="/about/">About</a>
<a href="/contact/">Contact</a>

<hr>

{% block content %}
{% endblock %}

<hr>

<h3>Copyright 2026</h3>
```

---

## templates/home.html
```html
{% extends 'layout.html' %}

{% block content %}
<h1>Home Page</h1>
{% endblock %}
```

---

## templates/about.html
```html
{% extends 'layout.html' %}

{% block content %}
<h1>About Us Page</h1>
{% endblock %}
```

---

## templates/contact.html
```html
{% extends 'layout.html' %}

{% block content %}
<h1>Contact Us Page</h1>
{% endblock %}
```

---

## lab2/urls.py
```py
from django.contrib import admin
from django.urls import path
from p2 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('home/', views.home),
    path('about/', views.about),
    path('contact/', views.contact),
]
```

---

# Program 3 & 4 (Many-to-Many Relationship)

## Folder Structure
```txt
lab34/
│── manage.py
│
├── lab34/
│   ├── settings.py
│   ├── urls.py
│
├── p34/
│   ├── admin.py
│   ├── models.py
│   ├── views.py
│
└── templates/
    └── home.html
```

## Create Project & App
```bash
django-admin startproject lab34
cd lab34
python manage.py startapp p34
```

---

## settings.py
Add inside `INSTALLED_APPS`

```py
'p34',
```

---

## p34/models.py
```py
from django.db import models

class Book(models.Model):
    book_name = models.CharField(max_length=100)

    def __str__(self):
        return self.book_name


class Student(models.Model):
    student_name = models.CharField(max_length=100)
    book = models.ManyToManyField(Book)

    def __str__(self):
        return self.student_name
```

---

## p34/admin.py
```py
from django.contrib import admin
from .models import Student, Book

admin.site.register(Student)
admin.site.register(Book)
```

---

## p34/views.py
```py
from django.shortcuts import render
from .models import Book, Student

def home(request):
    books = Book.objects.all()
    students = []

    if request.method == 'POST':
        book_id = request.POST.get('book')

        if book_id:
            selected_book = Book.objects.get(id=book_id)

            students = Student.objects.filter(
                book=selected_book
            )

    return render(
        request,
        'home.html',
        {
            'books': books,
            'students': students
        }
    )
```

---

## templates/home.html
```html
<h1>Library Management</h1>

<form method="POST">
    {% csrf_token %}

    Select Book:

    <select name="book">
        {% for i in books %}
            <option value="{{ i.id }}">
                {{ i.book_name }}
            </option>
        {% endfor %}
    </select>

    <button type="submit">
        Show Students
    </button>
</form>

<h2>Students</h2>

{% for i in students %}
    {{ i.student_name }}
    <br>
{% endfor %}
```

---

## lab34/urls.py
```py
from django.contrib import admin
from django.urls import path
from p34 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home),
]
```

---

## Migration Commands
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Create Superuser
```bash
python manage.py createsuperuser
```

---

## Run Server
```bash
python manage.py runserver
```

---

# Program 5 (Model Form)

## Folder Structure
```txt
lab5/
│── manage.py
│
├── lab5/
│   ├── settings.py
│   ├── urls.py
│
├── p5/
│   ├── models.py
│   ├── forms.py
│   ├── views.py
│
└── templates/
    └── project.html
```

## Create Project & App
```bash
django-admin startproject lab5
cd lab5
python manage.py startapp p5
```

---

## settings.py
Add inside `INSTALLED_APPS`

```py
'p5',
```

---

## p5/models.py
```py
from django.db import models

class Project(models.Model):
    student_name = models.CharField(max_length=100)
    topic = models.CharField(max_length=100)
    language = models.CharField(max_length=100)
    duration = models.IntegerField()

    def __str__(self):
        return self.student_name
```

---

## p5/forms.py
```py
from django import forms
from .models import Project

class ProjectForm(forms.ModelForm):

    class Meta:
        model = Project
        fields = '__all__'
```

---

## p5/views.py
```py
from django.shortcuts import render, redirect
from .forms import ProjectForm

def project(request):

    form = ProjectForm()

    if request.method == 'POST':
        form = ProjectForm(request.POST)

        if form.is_valid():
            form.save()
            return redirect('/project/')

    return render(
        request,
        'project.html',
        {
            'form': form
        }
    )
```

---

## templates/project.html
```html
<h1>Project Form</h1>

<form method="POST">
    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">
        Submit
    </button>
</form>
```

---

## lab5/urls.py
```py
from django.contrib import admin
from django.urls import path
from p5 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('project/', views.project),
]
```

---

# Program 6 (Class Based CRUD)

## Folder Structure
```txt
lab6/
│── manage.py
│
├── lab6/
│   ├── settings.py
│   ├── urls.py
│
├── p6/
│   ├── admin.py
│   ├── models.py
│   ├── views.py
│
└── templates/
    ├── student_list.html
    ├── student_detail.html
    ├── student_form.html
    └── student_delete.html
```

## Create Project & App
```bash
django-admin startproject lab6
cd lab6
python manage.py startapp p6
```

---

## settings.py
```py
'p6',
```

---

## p6/models.py
```py
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    course = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```

---

## p6/views.py
```py
from django.views.generic import (
    ListView,
    DetailView,
    CreateView,
    UpdateView,
    DeleteView
)

from .models import Student


class StudentList(ListView):
    model = Student
    template_name = 'student_list.html'


class StudentDetail(DetailView):
    model = Student
    template_name = 'student_detail.html'


class StudentCreate(CreateView):
    model = Student
    fields = '__all__'
    template_name = 'student_form.html'
    success_url = '/list/'


class StudentUpdate(UpdateView):
    model = Student
    fields = '__all__'
    template_name = 'student_form.html'
    success_url = '/list/'


class StudentDelete(DeleteView):
    model = Student
    template_name = 'student_delete.html'
    success_url = '/list/'
```

---

## p6/admin.py
```py
from django.contrib import admin
from .models import Student

admin.site.register(Student)
```

---

## templates/student_list.html
```html
<h1>Student List</h1>

<a href="/create/">
    Add Student
</a>

<br><br>

{% for i in object_list %}

    {{ i.name }}

    <a href="/detail/{{ i.id }}/">
        View
    </a>

    <a href="/update/{{ i.id }}/">
        Edit
    </a>

    <a href="/delete/{{ i.id }}/">
        Delete
    </a>

    <br><br>

{% endfor %}
```

---

## templates/student_detail.html
```html
<h1>Student Detail</h1>

Name: {{ object.name }}

<br><br>

Course: {{ object.course }}
```

---

## templates/student_form.html
```html
<h1>Student Form</h1>

<form method="POST">

    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">
        Submit
    </button>

</form>
```

---

## templates/student_delete.html
```html
<h1>Delete Student</h1>

<form method="POST">

    {% csrf_token %}

    <button type="submit">
        Confirm Delete
    </button>

</form>
```

---

## lab6/urls.py
```py
from django.contrib import admin
from django.urls import path
from p6 import views

urlpatterns = [
    path('admin/', admin.site.urls),

    path(
        'list/',
        views.StudentList.as_view()
    ),

    path(
        'detail/<int:pk>/',
        views.StudentDetail.as_view()
    ),

    path(
        'create/',
        views.StudentCreate.as_view()
    ),

    path(
        'update/<int:pk>/',
        views.StudentUpdate.as_view()
    ),

    path(
        'delete/<int:pk>/',
        views.StudentDelete.as_view()
    ),
]
```

---

# Program 7 (CSV & PDF Download)

## Folder Structure
```txt
lab7/
│── manage.py
│
├── lab7/
│   ├── settings.py
│   ├── urls.py
│
├── p7/
│   ├── admin.py
│   ├── models.py
│   ├── views.py
```

## Create Project & App
```bash
django-admin startproject lab7
cd lab7
python manage.py startapp p7
```

---

## settings.py
```py
'p7',
```

---

## p7/models.py
```py
from django.db import models

class Project(models.Model):
    student_name = models.CharField(max_length=100)
    topic = models.CharField(max_length=100)
    language = models.CharField(max_length=100)
    duration = models.IntegerField()

    def __str__(self):
        return self.student_name
```

---

## p7/admin.py
```py
from django.contrib import admin
from .models import Project

admin.site.register(Project)
```

---

## p7/views.py
```py
from django.http import HttpResponse
from .models import Project
import csv


def csvfile(request):

    response = HttpResponse(
        content_type='text/csv'
    )

    response[
        'Content-Disposition'
    ] = 'attachment; filename="project.csv"'

    writer = csv.writer(response)

    writer.writerow([
        'Name',
        'Topic',
        'Language',
        'Duration'
    ])

    data = Project.objects.all()

    for i in data:
        writer.writerow([
            i.student_name,
            i.topic,
            i.language,
            i.duration
        ])

    return response


def pdffile(request):

    response = HttpResponse(
        content_type='application/pdf'
    )

    response[
        'Content-Disposition'
    ] = 'attachment; filename="project.pdf"'

    response.write(
        "PDF Generated Successfully"
    )

    return response
```

---

## lab7/urls.py
```py
from django.contrib import admin
from django.urls import path
from p7 import views

urlpatterns = [
    path('admin/', admin.site.urls),

    path(
        'csv/',
        views.csvfile
    ),

    path(
        'pdf/',
        views.pdffile
    ),
]
```

---

# Program 8 (Authentication System)

## Folder Structure
```txt
lab8/
│── manage.py
│
├── lab8/
│   ├── settings.py
│   ├── urls.py
│
├── p8/
│   ├── views.py
│
└── templates/
    ├── signup.html
    ├── login.html
    └── home.html
```

## Create Project & App
```bash
django-admin startproject lab8
cd lab8
python manage.py startapp p8
```

---

## settings.py
```py
'p8',
```

---

## p8/views.py
```py
from django.shortcuts import render, redirect

from django.contrib.auth.forms import UserCreationForm

from django.contrib.auth import (
    authenticate,
    login,
    logout
)


def signup(request):

    if request.method == 'POST':

        form = UserCreationForm(request.POST)

        if form.is_valid():
            form.save()
            return redirect('/login/')

    else:
        form = UserCreationForm()

    return render(
        request,
        'signup.html',
        {
            'form': form
        }
    )


def userlogin(request):

    if request.method == 'POST':

        username = request.POST.get('username')

        password = request.POST.get('password')

        user = authenticate(
            request,
            username=username,
            password=password
        )

        if user is not None:
            login(request, user)
            return redirect('/home/')

    return render(
        request,
        'login.html'
    )


def userlogout(request):
    logout(request)
    return redirect('/login/')


def home(request):
    return render(
        request,
        'home.html'
    )
```

---

## templates/signup.html
```html
<h1>Signup Page</h1>

<form method="POST">

    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">
        Register
    </button>

</form>

<a href="/login/">
    Login
</a>
```

---

## templates/login.html
```html
<h1>Login Page</h1>

<form method="POST">

    {% csrf_token %}

    Username:
    <input type="text" name="username">

    <br><br>

    Password:
    <input type="password" name="password">

    <br><br>

    <input type="submit" value="Login">

</form>
```

---

## templates/home.html
```html
<h1>
    Login Successful
</h1>

<a href="/logout/">
    Logout
</a>
```

---

## lab8/urls.py
```py
from django.contrib import admin
from django.urls import path
from p8 import views

urlpatterns = [
    path('admin/', admin.site.urls),

    path(
        'signup/',
        views.signup
    ),

    path(
        'login/',
        views.userlogin
    ),

    path(
        'logout/',
        views.userlogout
    ),

    path(
        'home/',
        views.home
    ),
]
```

---

# Common Commands

## Run Migration
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Create Superuser
```bash
python manage.py createsuperuser
```

---

## Run Server
```bash
python manage.py runserver
```