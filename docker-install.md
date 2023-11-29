# Install Docker

## On Centos 8

```
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

```
dnf install docker-ce docker-ce-cli containerd.io
```

```
systemctl enable --now docker
```

## On Centos 7

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

```
yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

```
yum install docker-ce
```

```
systemctl start docker
```

```
systemctl enable docker
```
