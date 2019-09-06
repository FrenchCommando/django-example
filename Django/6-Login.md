urls.py

```python
from django.contrib import admin
from django.contrib.auth import views as auth_views
from django.urls import path, include
from users import views as user_views


urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
    path('register/', user_views.register, name='register'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
]

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
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Sign Up</button>
      </div>
    </form>
    <div class="border-top pt-3">
      <small class="text-muted">
        Already have an account? <a class="ml-2" href="{% url 'login' %}">Sign In</a>
      </small>
    </div>
  </div>
{% endblock content %}

```

new login.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Log In</legend>
        {{ form|crispy }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Login</button>
      </div>
    </form>
    <div class="border-top pt-3">
      <small class="text-muted">
        Need an account? <a class="ml-2" href="{% url 'register' %}">Sign Up</a>
      </small>
    </div>
  </div>
{% endblock content %}

```

settings.py

```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/2.2/howto/static-files/

STATIC_URL = '/static/'

CRISPY_TEMPLATE_PACK = 'bootstrap4'

LOGIN_REDIRECT_URL = 'myapp-home'

```



default logout takes to admin logout



views.py - change message

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
            # messages.success(request, f'Account created for {username}!')
            messages.success(request, f'Your account has been created! You are now able to log in!')
            return redirect('login')
    else:
        form = UserRegisterForm()
    return render(request, 'users/register.html', {'form': form})

```



logout.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
  <h2>You have been logged out!</h2>
  <div class="border-top pt-3">
    <small class="text-muted">
      <a href="{% url 'login' %}">Log In Again</a>
    </small>
  </div>
{% endblock content %}

```



base.html - condition on login

```html
<header class="site-header">
      <nav class="navbar navbar-expand-md navbar-dark bg-steel fixed-top">
        <div class="container">
          <a class="navbar-brand mr-4" href="{% url 'myapp-home' %}">Django Blog</a>
          <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarToggle" aria-controls="navbarToggle" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarToggle">
            <div class="navbar-nav mr-auto">
              <a class="nav-item nav-link" href="{% url 'myapp-home' %}">Home</a>
              <a class="nav-item nav-link" href="{% url 'myapp-about' %}">About</a>
            </div>
            <!-- Navbar Right Side -->
            <div class="navbar-nav">
              {% if user.is_authenticated %}
              <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
              {% else %}
              <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
              <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
              {% endif %}
            </div>
          </div>
        </div>
      </nav>
    </header>
```



## Restriction - login only page

profile.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <h1>{{ user.username }}</h1>
{% endblock content %}

```

users/views.py

```python
def profile(request):
    return render(request, 'users/profile.html')
```

myprojects/urls.py

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
    path('register/', user_views.register, name='register'),
    path('profile/', user_views.profile, name='profile'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
]

```

base.html

```html
              {% if user.is_authenticated %}
              <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
              <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
              {% else %}
              <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
              <a class="nav-i
```



decorator in views.py

```python
from django.contrib.auth.decorators import login_required


@login_required
def profile(request):
    return render(request, 'users/profile.html')
```

settings.py

```python

LOGIN_REDIRECT_URL = 'myapp-home'
LOGIN_URL = 'login'

```

