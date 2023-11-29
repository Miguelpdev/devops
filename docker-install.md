# Install Docker

## On Centos 8

### CentOS Linux 8 - AppStream

```
udo sed -i -e "s|mirrorlist=|#mirrorlist=|g" /etc/yum.repos.d/CentOS-*
```

```
sudo sed -i -e "s|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g" /etc/yum.repos.d/CentOS-*
```

```
dnf clean all
dnf install app
```

### Install

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
