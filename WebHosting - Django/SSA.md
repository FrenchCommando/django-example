Create folder on remote server

```shell
pwd
mkdir -p ~/.ssh
ls -la
```

Create key on local computer

```shell
ssh-keygen -b 4096
```

Copy public key

```shell
scp ~/.ssh/id_rsa.pub username@remotehost:~/.ssh/authorized_keys
```

Change permissions

```shell
ls .ssh
sudo chmod 700 ~/.ssh/
sudo chmod 600 ~/.ssh/*
```



Change configs

```shell
sudo nano /etc/ssh/sshd_config
```

```shell
PermitRootLogin no
...
PasswordAuthentication no
```



Restart

```shell
sudo systemctl restart ssh
```



