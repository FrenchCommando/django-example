uncomplicated firewall

```shell
sudo apt-get install ufw
```

```shell
sudo ufw default allow outgoing
```

```shell
sudo ufw default deny incoming
```

```shell
sudo ufw allow ssh
```

- allow custom port

```shell
sudo ufw allow 8000
```

```shell
sudo ufw enable
```

```shell
sudo ufw status
```

