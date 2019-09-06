forms.py

```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm
from .models import Profile

...

class UserUpdateForm(forms.ModelForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email']


class ProfileUpdateForm(forms.ModelForm):
    class Meta:
        model = Profile
        field = ['image']

```



views.py

```python
@login_required
def profile(request):
    u_form = UserUpdateForm()
    p_form = ProfileUpdateForm()

    context = {
        'u_form': u_form,
        'p_form': p_form,
    }
    
    return render(request, 'users/profile.html', context)

```



profile.html from register.html

```html
...
    <form method="POST" enctype="multipart/form-data">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Profile Info</legend>
        {{ u_form|crispy }}
        {{ p_form|crispy }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Update</button>
      </div>
    </form>
  </div>
{% endblock content %}

```



info from form -> views.py

```python
@login_required
def profile(request):
    if request.method == 'POST':
        u_form = UserUpdateForm(request.POST, instance=request.user)
        p_form = ProfileUpdateForm(request.POST,
                                   request.FILES,
                                   instance=request.user.profile)
        if u_form.is_valid() and p_form.is_valid():
            u_form.save()
            p_form.save()
            messages.success(request, f'Your account has been updated')
            return redirect('profile')
    else:
        u_form = UserUpdateForm(instance=request.user)
        p_form = ProfileUpdateForm(instance=request.user.profile)

    context = {
        'u_form': u_form,
        'p_form': p_form,
    }

    return render(request, 'users/profile.html', context)
```



using Pillow for resize image

models.py - override save

```python
from django.db import models
from django.contrib.auth.models import User
from PIL import Image


class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    image = models.ImageField(default='default.jpg', upload_to='profile_pics')

    def __str__(self):
        return f'{self.user.username} Profile'

    def save(self):
        super().save()
        img = Image.open(self.image.path)
        if img.height > 300 or img.width > 300:
            output_size = (300, 300)
            img.thumbnail(output_size)
            img.save(self.image.path)

```

add pictures in post

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
          <h2><a class="article-title" href="#">{{ post.title }}</a></h2>
          <p class="article-content">{{ post.content }}</p>
        </div>
      </article>
    {% endfor %}
{% endblock content %}

```

