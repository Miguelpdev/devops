# Configuraciones cuando el server se haya apagado

## Conexion al servidor

```
ping example.lamolina.edu.pe
```

## Verificar servicio de MySql

```
ssh root@bd-test.lamolina.edu.pe
```

```
systemctl status mysqld
```

En caso este inactivo iniciar el servicio

```
systemctl start mysqld
```

## Reiniciar el aplicacion web desde jenkins

## En caso haya problemas con redis

```
tail -f /var/log/jenkins/------.log
```

```
redis-cli flushall
```

# Reinicio de servidores sin Jenkins

### Verificar si la app esta corriendo y si existe eliminarlo

```
jcmd
kill -9 (pid)
```

### Primero tener el archivo .jar

```
ls /lost+found
```

### Ejecutarlo con nohup

```
export USERDB=usuario
export PASSWORD=password
```

#### Para test

```
nohup java -jar archivo.jar --spring.profiles.active=test > /dev/null 2>&1 &
```

#### Para Produccion

```
nohup java -jar archivo.jar --spring.profiles.active=prod > /dev/null 2>&1 &
```
