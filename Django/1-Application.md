```shell
python manage.py startapp myapp
```

- in myapp/views.py

```python
from django.shortcuts import render
from django.http import HttpResponse

def home(request):
    return HttpResponse('<h1>Blog Home</h1>')
```

- in myapp/urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='blog-home'),
]
```



- in myproject/urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('myapp/', include('myapp.urls')),
]
```



# Adding about page

- in myapp/views.py

```python
def about(request):
    return HttpResponse('<h1>MyApp About</h1>')
```

- in myapp/urls.py

```python
urlpatterns = [
    path('', views.home, name='myapp-home'),
    path('about/', views.about, name='myapp-about'),
]
```

