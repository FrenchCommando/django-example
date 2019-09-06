models.py in users

```python
from django.db import models
from django.contrib.auth.models import User


class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    image = models.ImageField(default='default.jpg', upload_to='profile_pics')

    def __str__(self):
        return f'{self.user.username} Profile'

```



install Pillow

```shell
pip install Pillow
python manage.py makemigrations
python manage.py migrate
```



users - admin.py

```python
from django.contrib import admin
from .models import profile

admin.site.register(Profile)

```



python manage.py shell

```python
>>> from django.contrib.auth.models import User
>>> user = User.objects.filter(username='martial').first()
>>> user
<User: martial>
>>> user.profile
<Profile: martial Profile>
>>> user.profile.image
<ImageFieldFile: profile_pics/20190810.jpg>
>>> user.profile.image.width
1920
>>> user.profile.image.url
'profile_pics/20190810.jpg'
```

settings.py -> profile_pics folder in media subfolder

```python
STATIC_URL = '/static/'

MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'

CRISPY_TEMPLATE_PACK = 'bootstrap4'

LOGIN_REDIRECT_URL = 'myapp-home'
LOGIN_URL = 'login'
```



urls.py

```python
from django.conf import settings
from django.conf.urls.static import static

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

```



default image

users - signals.py

```python
from django.db.models.signals import post_save
from django.contrib.auth.models import User
from django.dispatch import receiver
from .models import Profile


@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)


@receiver(post_save, sender=User)
def save_profile(sender, instance, **kwargs):
    instance.profile.save()

```

apps.py

```python
from django.apps import AppConfig


class UsersConfig(AppConfig):
    name = 'users'

    def ready(self):
        import user.signals

```



