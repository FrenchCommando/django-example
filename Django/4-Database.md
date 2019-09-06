myapp/models.py

```python
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User


class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # date_posted = models.DateTimeField(auto_now=True)  # when is post created
    date_posted = models.DateTimeField(default=timezone.now)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    # delete the post if user is deleted

```



```shell
python manage.py makemigrations
python manage.py sqlmigrate myapp 0001
python manage.py migrate
```

```shell
python manage.py shell
>>>
```

```python
Python 3.7.3 (default, Apr  3 2019, 05:39:12) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from myapp.models import Post
>>> from django.contrib.auth.models import User
>>> User.objects.all()
<QuerySet [<User: frenchcommando>, <User: TestUser>]>
>>> User.objects.first()
<User: frenchcommando>
>>> User.objects.last()
<User: TestUser>
>>> User.objects.filter(username='frenchcommando')
<QuerySet [<User: frenchcommando>]>
>>> User.objects.filter(username='frenchcommando').first()
<User: frenchcommando>
>>> user = User.objects.filter(username='frenchcommando').first()
>>> user
<User: frenchcommando>
>>> user.id
1
>>> user.pk
1
>>> user = User.objects.get(id=1)
>>> user
<User: frenchcommando>
>>> Post.objects.all()
<QuerySet []>
>>> post_1 = Post(title='MyApp 1', content='First Post Content!', author=user)
>>> Post.objects.all()
<QuerySet []>
>>> post_1.save()
>>> Post.objects.all()
<QuerySet [<Post: Post object (1)>]>
```



# Implementing \__str__

```python
class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # date_posted = models.DateTimeField(auto_now=True)  # when is post created
    date_posted = models.DateTimeField(default=timezone.now)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    # delete the post if user is deleted

    def __str__(self):
        return self.title
```

```python
>>> from myapp.models import Post
>>> from django.contrib.auth.models import User
>>> Post.objects.all()
<QuerySet [<Post: MyApp 1>]>
```



## More Commands

```python
>>> from myapp.models import Post
>>> from django.contrib.auth.models import User
>>> Post.objects.all()
<QuerySet [<Post: MyApp 1>]>
>>> user = User.objects.filter(username='frenchcommando').first()
>>> user
<User: frenchcommando>
>>> post_2 = Post(title='MyApp 2', content='Second Post Content!', author_id=user.id)
>>> post_2.save()
>>> Post.objects.all()
<QuerySet [<Post: MyApp 1>, <Post: MyApp 2>]>
>>> post = Post.objects.first()
>>> post.content
'First Post Content!'
>>> post.date_posted
datetime.datetime(2019, 8, 30, 9, 28, 8, 616773, tzinfo=<UTC>)
>>> post.author
<User: frenchcommando>
>>> post.author.email
'martialren@gmail.com'
```

- posts from same user

```python
>>> user
<User: frenchcommando>
>>> user.post_set
<django.db.models.fields.related_descriptors.create_reverse_many_to_one_manager.<locals>.RelatedManager object at 0x7f04297f9e48>
>>> user.post_set.all()
<QuerySet [<Post: MyApp 1>, <Post: MyApp 2>]>
>>> user.post_set.create(title='MyApp 3', content='Third Post Content!')
<Post: MyApp 3>
>>> Post.objects.all()
<QuerySet [<Post: MyApp 1>, <Post: MyApp 2>, <Post: MyApp 3>]>
```





## Using our database

```python
from django.shortcuts import render
from .models import Post


def home(request):
    context = {
        'posts': Post.objects.all()
    }
    # return HttpResponse('<h1>MyApp Home</h1>')
    return render(request, 'myapp/home.html', context)
```



## Date format

home.html

https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#std:templatefilter-date

no spaces

```python
<small class="text-muted">{{ post.date_posted|date:"F d, Y" }}</small>
```



## Register Models in Admin Page

admin.py

```python
from django.contrib import admin
from .models import Post


admin.site.register(Post)
```

