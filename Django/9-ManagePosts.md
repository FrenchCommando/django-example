views.py -> replace home

```python
from django.shortcuts import render
from django.views.generic import ListView
from .models import Post


def home(request):
    context = {
        'posts': Post.objects.all()
    }
    # return HttpResponse('<h1>MyApp Home</h1>')
    return render(request, 'myapp/home.html', context)


class PostListView(ListView):
    model = Post
    template_name = 'myapp/home.html'  # <app>/<model>_<viewtype>.html
    context_object_name = 'posts'
    ordering = ['-date_posted']  # '-' for reverse


def about(request):
    return render(request, 'myapp/about.html', {'title': 'About'})
    # return HttpResponse('<h1>MyApp About</h1>')

```



urls.py

```python
from django.urls import path
from .views import PostListView
from . import views

urlpatterns = [
    # path('', views.home, name='myapp-home'),
    path('', PostListView.as_view(), name='myapp-home'),
    path('about/', views.about, name='myapp-about'),
]

```





# Detail view

urls.py

```python
from django.urls import path
from .views import PostListView, PostDetailView
from . import views


urlpatterns = [
    path('', PostListView.as_view(), name='myapp-home'),
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('about/', views.about, name='myapp-about'),
]
```



views.py

```python
class PostDetailView(DetailView):
    model = Post
```

post_detail.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
    <article class="media content-section">
      <img class="rounded-circle article-img" src="{{ object.author.profile.image.url }}"
      <div class="media-body">
        <div class="article-metadata">
          <a class="mr-2" href="#">{{ object.author }}</a>
          <small class="text-muted">{{ object.date_posted|date:"F d, Y" }}</small>
        </div>
        <h2 class="article-title">{{ object.title }}</h2>
        <p class="article-content">{{ object.content }}</p>
      </div>
    </article>
{% endblock content %}

```



localhots:8000/post/1/

home.html

```html
          <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>

```





# Create Post

views.py

```python
from django.shortcuts import render
from django.views.generic import ListView, DetailView, CreateView

...

class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

```

urls.py

```python
from django.urls import path
from .views import PostListView, PostDetailView, PostCreateView
from . import views


urlpatterns = [
    path('', PostListView.as_view(), name='myapp-home'),
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('about/', views.about, name='myapp-about'),
]

```

post_form.html <- from register.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Blog Post</legend>
        {{ form|crispy }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Post</button>
      </div>
    </form>
  </div>
{% endblock content %}

```

models.py

```python
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User
from django.urls import reverse


class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # date_posted = models.DateTimeField(auto_now=True)  # when is post created
    date_posted = models.DateTimeField(default=timezone.now)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    # delete the post if user is deleted

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse('post-detail', kwargs={'pk': self.pk})

```



# Update

urls.py

```python
from django.urls import path
from .views import PostListView, PostDetailView, PostCreateView, PostUpdateView
from . import views


urlpatterns = [
    path('', PostListView.as_view(), name='myapp-home'),
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('post/<int:pk>/update/', PostUpdateView.as_view(), name='post-update'),
    path('about/', views.about, name='myapp-about'),
]

```

views.py

```python
from django.shortcuts import render
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin
from django.views.generic import ListView, DetailView, CreateView, UpdateView
from .models import Post

...

class PostUpdateView(LoginRequiredMixin, UserPassesTestMixin, UpdateView):
    model = Post
    fields = ['title', 'content']

    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

    def test_func(self):
        post = self.get_object()
        if self.request.user == post.author:
            return True
        return False

```



# Delete

urls.py

```python
from django.urls import path
from .views import PostListView, PostDetailView, PostCreateView, PostUpdateView, PostDeleteView
from . import views


urlpatterns = [
    path('', PostListView.as_view(), name='myapp-home'),
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('post/<int:pk>/update/', PostUpdateView.as_view(), name='post-update'),
    path('post/<int:pk>/delete/', PostDeleteView.as_view(), name='post-delete'),
    path('about/', views.about, name='myapp-about'),
]

```

views.py

```python
class PostDeleteView(LoginRequiredMixin, UserPassesTestMixin, DeleteView):
    model = Post
    success_url = '/'

    def test_func(self):
        post = self.get_object()
        if self.request.user == post.author:
            return True
        return False
        

```

post_confirm_delete.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Delete Post</legend>
        <h2>Are you sure you want to delete the post "{{ object.title }}" ?</h2>
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-danger" type="submit">Yes, Delete</button>
        <a class="btn btn-outline-secondary" href="{% url 'post-detail' object.id %}">Cancel</a>
      </div>
    </form>
  </div>
{% endblock content %}

```



# Links

base.html

```html
            <div class="navbar-nav">
              {% if user.is_authenticated %}
              <a class="nav-item nav-link" href="{% url 'post-create' %}">New Post</a>
              <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
              <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
              {% else %}
              <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
              <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
              {% endif %}
            </div>
```

post_detail.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
    <article class="media content-section">
      <img class="rounded-circle article-img" src="{{ object.author.profile.image.url }}"
      <div class="media-body">
        <div class="article-metadata">
          <a class="mr-2" href="#">{{ object.author }}</a>
          <small class="text-muted">{{ object.date_posted|date:"F d, Y" }}</small>
          {% if object.author == user %}
            <div class="">
              <a class="btn btn-secondary btn-sm mt-1 mb-1" href="{% url 'post-update' object.id %}">Update</a>
              <a class="btn btn-danger btn-sm mt-1 mb-1" href="{% url 'post-delete' object.id %}">Delete</a>              
            </div>
          {% endif %}
        </div>
        <h2 class="article-title">{{ object.title }}</h2>
        <p class="article-content">{{ object.content }}</p>
      </div>
    </article>
{% endblock content %}

```



