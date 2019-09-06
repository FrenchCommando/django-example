```shell
scp -r project hostname@serveraddress:~/
```

-r is for recursive



```shell
sudo nano directory/setting.py
```

content

```shell
DEBUG = True
ALLOWED_HOSTS = ['myipaddress']  # add your address
...
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'
...
```

```shell
python manage.py collectstatic
ls
```

```shell
python manage.py runserver 0.0.0.0:8000
```

