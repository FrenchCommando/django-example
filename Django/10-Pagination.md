views.py

```python
class PostListView(ListView):
    model = Post
    template_name = 'myapp/home.html'  # <app>/<model>_<viewtype>.html
    context_object_name = 'posts'
    ordering = ['-date_posted']  # '-' for reverse
    paginate_by = 2


```

http://localhost:8000/?page=2



home.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
    {% for post in posts %}
      <article class="media content-section">
        <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}"
        <div class="media-body">
          <div class="article-metadata">
            <a class="mr-2" href="#">{{ post.author }}</a>
            <small class="text-muted">{{ post.date_posted|date:"F d, Y" }}</small>
          </div>
          <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
          <p class="article-content">{{ post.content }}</p>
        </div>
      </article>
    {% endfor %}
    {% if is_paginated %}
      {% if page_obj.has_previous %}
        <a class="btn btn-outline-info mb-4" href="?page=1">First</a>
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.previous_page_number }}">Previous</a>
      {% endif %}

      {% for num in page_obj.paginator.page_range %}
        {% if page_obj.number == num %}
        <a class="btn btn-info mb-4" href="?page={{ num }}">{{ num }}</a>
        {% elif num > page_obj.number|add:'-3' and num < page_obj.number|add:'3' %}
        <a class="btn btn-outline-info mb-4" href="?page={{ num }}">{{ num }}</a>
        {% endif %}
      {% endfor %}

      {% if page_obj.has_next %}
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.next_page_number }}">Next</a>
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
      {% endif %}
    {% endif %}
{% endblock content %}

```



# Link to posts of given user

views.py

```python
from django.shortcuts import render, get_object_or_404
from django.contrib.auth.models import User
class UserPostListView(ListView):
    model = Post
    template_name = 'myapp/user_posts.html'  # <app>/<model>_<viewtype>.html
    context_object_name = 'posts'
    paginate_by = 5

    def get_queryset(self):
        user = get_object_or_404(User, username=self.kwargs.get('username'))
        return Post.objects.filter(author=user).order_by('-date_posted')

```



urls.py

```python
from django.urls import path
from .views import PostListView, PostDetailView, PostCreateView, PostUpdateView, PostDeleteView, UserPostListView
from . import views


urlpatterns = [
    path('', PostListView.as_view(), name='myapp-home'),
    path('user/<str:username>', UserPostListView.as_view(), name='user-posts'),
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('post/<int:pk>/update/', PostUpdateView.as_view(), name='post-update'),
    path('post/<int:pk>/delete/', PostDeleteView.as_view(), name='post-delete'),
    path('about/', views.about, name='myapp-about'),
]

```



user_posts.html <- from home.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
  <h1 class="mb-3">Posts by {{ view.kwargs.username }} ({{ page_obj.paginator.count }})</h1>
    {% for post in posts %}
      <article class="media content-section">
        <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}"
        <div class="media-body">
          <div class="article-metadata">
            <a class="mr-2" href="{% url 'user-posts' post.author.username %}">{{ post.author }}</a>
            <small class="text-muted">{{ post.date_posted|date:"F d, Y" }}</small>
          </div>
          <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
          <p class="article-content">{{ post.content }}</p>
        </div>
      </article>
    {% endfor %}
    {% if is_paginated %}
      {% if page_obj.has_previous %}
        <a class="btn btn-outline-info mb-4" href="?page=1">First</a>
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.previous_page_number }}">Previous</a>
      {% endif %}

      {% for num in page_obj.paginator.page_range %}
        {% if page_obj.number == num %}
        <a class="btn btn-info mb-4" href="?page={{ num }}">{{ num }}</a>
        {% elif num > page_obj.number|add:'-3' and num < page_obj.number|add:'3' %}
        <a class="btn btn-outline-info mb-4" href="?page={{ num }}">{{ num }}</a>
        {% endif %}
      {% endfor %}

      {% if page_obj.has_next %}
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.next_page_number }}">Next</a>
        <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
      {% endif %}
    {% endif %}
{% endblock content %}

```

link in home.html

```html
{% url 'user-posts' post.author.username %}
```

in post_detail.html

```html
{% url 'user-posts' object.author.username %}
```

