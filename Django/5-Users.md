```shell
python manage.py startapp users
```

from apps.py -> settings.py

```python
INSTALLED_APPS = [
    'myapp.apps.MyappConfig',
    'users.apps.UsersConfig',
```

views.py

```python
from django.shortcuts import render
from django.contrib.auth.forms import UserCreationForm


def register(request):
    form = UserCreationForm()
    return render(request, 'users/register.html', {'form': form})

```



register.html

```python
{%  extends "myapp/base.html" %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Join Today</legend>
        {{ form }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Sign Up</button>
      </div>
    </form>
    <div class="border-top pt-3">
      <small class="text-muted">
        Already have an account? <a class="ml-2" href="#">Sign In</a>
      </small>
    </div>
  </div>
{% endblock content %}

```



myproject/urls.py

```python
from django.contrib import admin
from django.urls import path, include
from users import views as user_views


urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
    path('register/', user_views.register, name='register')
]

```



register.html

as_p - paragraphs

```html
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Join Today</legend>
        {{ form.as_p }}
      </fieldset>
```



# Use info from form

views.py

```python
from django.shortcuts import render, redirect
from django.contrib.auth.forms import UserCreationForm
from django.contrib import messages


def register(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data.get('username')
            messages.success(request, f'Account created for {username}!')
            return redirect('myapp-home')
    else:
        form = UserCreationForm()
    return render(request, 'users/register.html', {'form': form})

```



base.html

```html
        <div class="col-md-8">
          {% if messages %}
            {% for message in messages %}
              <div class="alert alert-{{ message.tags }}">
                {{ message }}
              </div>
            {% endfor %}
          {% endif %}
          {% block content %}{% endblock %}
        </div>
```



# New Form with Email

users/forms.py - new file

```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm


class UserRegisterForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']

```

views.py

```python
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import UserRegisterForm


def register(request):
    if request.method == 'POST':
        form = UserRegisterForm(request.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            messages.success(request, f'Account created for {username}!')
            return redirect('myapp-home')
    else:
        form = UserRegisterForm()
    return render(request, 'users/register.html', {'form': form})

```



# Crispy Forms

```shell
pip install django-crispy-forms
```

settings.py

```python
INSTALLED_APPS = [
    'myapp.apps.MyappConfig',
    'users.apps.UsersConfig',
    'crispy_forms',
    
...    

CRISPY_TEMPLATE_PACK = 'bootstrap4'

```



register.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Join Today</legend>
        {{ form|crispy }}
  ...
          
```

