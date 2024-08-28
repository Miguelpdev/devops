# NGINX

## Instalar nginx

`yum install nginx`

## Configurara nginx

`vim /etc/nginx/nginx.conf`

```
log_format servlog '$server_name to: $upstream_addr [$request] '
        'upstream_response_time $upstream_response_time '
        'msec $msec request_time $request_time '
```

```
upstream backend{
        ip_hash;
        server 172.16.0.ip1:9001;
        server 172.16.0.ip2:9002;
    }
```

```
server_name  maipi-test.lamolina.edu.pe;
```

```
location / {
                proxy_pass http://backend;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
```

## Instalar Snapd

`yum install snapd`

`systemctl enable --now snapd.socket`

`ln -s /var/lib/snapd/snap /snap`

`sudo snap install core; sudo snap refresh core`

`snap install --classic certbot`

`ln -s /snap/bin/certbot /usr/bin/certbot`

Por ejemplo, puedes ejecutar el comando grep nginx /var/log/audit/audit.log | audit2allow -M nginx-custom para generar una política personalizada basada en los registros de auditoría relacionados con NGINX y luego aplicarla con semodule -i nginx-custom.pp.

### Permitir acceso de puertos nginx

`grep nginx /var/log/audit/audit.log | audit2allow -M nginx-custom`

`semodule -i nginx-custom.pp`

## **Configurar SSL**

`certbot --nginx`

[X] A
