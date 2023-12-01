# Scripts

## DNS

```
vim /etc/resolv.conf
ingresar al principio
nameserver 172.16.50.146
```

```
vim /etc/hosts
```

```
nohup java -jar amauta.jar --spring.profiles.active=devel > /dev/null 2 >&1 &
```

## necesita reiniciar DNS

```
systemctl restart named
```

## cambiar version de java en centos 7

```
alternatives --config java
```

## para eliminar las bd primero setear a 0

```
SET foreign_key_checks = 0;
drop database lamolina;
drop database octopus;
drop database recaudacion;
SET foreign_key_checks = 1;
```

## Para restaurar una bd ubicarse en la direccion del archivo

```
mysql -f -u root -pmysql mysql < sigu.sql
mysql -f -u root -p1234 mysql < sigu-2023-0320-0301.sql
```

## Cambiar contrasena local mysql

```
alter user 'root@'localhost' identified by '1234';
```

## Dar permiso de cualquier pc

```
grant all on _._ to root@'%' indentified by 'contrasena'
```

## para ver disco

```
cat /sys/block/sda/queue/rotational -- para ver sis tiene disco solido = 0
```

### restaurar bd

1: crear primero bd miguel

```
mysql -u root -p miguel < sigu-2023-0227-0720/lamolina.sql
```

### crear backup

```
mysqldump --user root --password lamolina > lamolina.sql
```

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
```

```
docker run -d -p 9100:9100 -v /root/recaudacion/node_exporter.yml:/etc/node_exporter/node_exporter.yml --name=node-exporter prom/node-exporter --web.listen-address="0.0.0.0:9100"
```

## Conectarse por ssh sin paswword

Desde el lado cliente, no ingresar niguna frase

```
ssh-keygen -t rsa
```

```
ssh-copy-id -i /root/.ssh/id_rsa.pub adminadunalm@172.16.0.108
```

## Para copiar id desde cliente a servidor

```
scp /var/jenkins_home/workspace/amanita-prod/target/amauta.jar adminadunalm@172.16.0.108:/home/adminadunalm/jenkins
```

```
sudo firewall-cmd --list-ports
```

## Redis

```
vim /etc/redis.conf
```

### cambiar lo siguiente

```
bind 0.0.0.0
requirepass tu_nueva_contraseña
```

```
redis-cli -h 172.16.50.156 -a password
```

## Para ver puertos abiertos

```
ss -plnt
```
