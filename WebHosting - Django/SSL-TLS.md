Let's Encrypt

```shell
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-apache

sudo certbot --apache

sudo certbot renew --dry-run
```





```shell
sudo ufw allow https
```

```shell
sudo certbot renew --dry-run
```





### Setup cron for certificate renewal

```shell
sudo crontab -e
```

```shell
30 4 1 * * sudo certbot renew --quiet
```

