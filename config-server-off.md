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

## Reiniciar el servicio de jenkins
