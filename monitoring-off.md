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
