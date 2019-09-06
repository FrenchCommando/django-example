```shell
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi-py3
```

```shell
cd /etc/apache2/sites-available/
ls
sudo cp 000-default.conf project.conf
sudo nano project.conf
```

```shell
...
	Alias /static /home/username/project/static
	<Directory /home/username/project/static>
		Require all granted
	</Directory>
	
	Alias /media /home/username/project/media
	<Directory /home/username/project/media>
		Require all granted
	</Directory>
	
	<Directory /home/username/project/project>
		<Files wsgi.py>
			Require all granted
		</Files>
	</Directory>
	
	WSGIScriptAlias / /homeusername/project/project/wsgi.py
	WSGIDaemonProcess django_app python-path=/home/username/project python-home=/home/username/project/venv
	WSGIProcessGroup django_app
	
</VirtualHost>
```



Run site

```shell
sudo a2ensite project.conf
sudo a2dissite 000-default.conf
```

Permissions for sqlite and media folder

```shell
sudo chown :www-data project/db.sqlite3
sudo chmod 664 project/db.sqlite3
sudo chown :www-data project/
ls -la
sudo chmod 775 project/

sudo chown -R :www-data project/media
sudo chmod -R 775 project/media
```

Config file

```shell
sudo touch /etc/config.json
sudo nano project/project/settings.py

​```python
import json
with open('/etc/config.json') as config_file:
	config = json.load(config_file)
	
SECRET_KEY = 'blahblahblah' -> config["SECRET_KEY"]
DEBUG = False
...
EMAIL_HOST_USER = config["EMAIL_USER"]
EMAIL_HOST_PASSWORD = config["EMAIL_PASS"]

sudo nano /etc/config.json
{
	"SECRET_KEY": "blahblahblah",
	"EMAIL_USER": "username",
	"EMAIL_PASS": "password"
}
```



Finally

```shell
sudo ufw delete allow 8000
sudo ufw allow http/tcp
sudo service apache2 restart
```



Configs

```shell
sudo apachectl configtest
```

