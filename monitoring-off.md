# Conectar los clientes al servidor de monitoreo

## Conectarse al cliente

```
ssh root@10.10.10.0
```

## Revisar si esta corriendo el node-exporter

```
docker ps
```

Para ver contenedores apagados

```
docker ps -a
```

## Iniciar el node exporter

```
docker start node-exporter
```

# Para activar el monitoreo de paginas web

```
ssh adminadunalm@ip_del_servidor_monitoreo
```

```
sudo -i
```

Contenedor que inicia el monitoreo de las paginas web

```
docker start blackbox
```

Contenedor que inicia el pagina principal de monitoreo

```
docker start monitor
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
