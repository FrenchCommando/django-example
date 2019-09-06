urls.py

```python

    path('password-reset/',
        auth_views.PasswordResetView.as_view(template_name='users/password_reset.html'),
        name='password_reset'
    ),
    path('password-reset/done/',
        auth_views.PasswordResetDoneView.as_view(template_name='users/password_reset_done.html'),
        name='password_reset_done'
    ),
    path('password-reset-confirm/<uidb64>/<token>/',
        auth_views.PasswordResetConfirmView.as_view(template_name='users/password_reset_confirm.html'),
        name='password_reset_confirm'
    ),
    path('password-reset-complete/',
        auth_views.PasswordResetCompleteView.as_view(template_name='users/password_reset_complete.html'),
        name='password_reset_complete'
    ),
]
```



password_reset.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Reset Password</legend>
        {{ form|crispy }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Request Password Reset</button>
      </div>
    </form>
  </div>
{% endblock content %}

```

password_reset_done.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
  <div class="alert alert-info">
    An email has been sent with instructions to reset your password.    
  </div>
{% endblock content %}

```

password_reset_confirm.html

```html
{%  extends "myapp/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
  <div class="content-section">
    <form method="POST">
      {% csrf_token %}
      <fieldset class="form-group">
        <legend class="border-bottom mb-4">Reset Password</legend>
        {{ form|crispy }}
      </fieldset>
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Reset Password</button>
      </div>
    </form>
  </div>
{% endblock content %}

```

password_reset_complete.html

```html
{%  extends "myapp/base.html" %}
{% block content %}
  <div class="alert alert-info">
    Your password has been set. 
  </div>
  <a href="{% url 'login' %}">Sign In Here</a>
{% endblock content %}

```

login.html

```html
      <div class="form-group">
        <button class="btn btn-outline-info" type="submit">Login</button>
        <small class="text-muted ml-2">
          <a href="{% url 'password_reset' %}">Forgot Password?</a>
        </small>
      </div>
```

