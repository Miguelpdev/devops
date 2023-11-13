## Documentación de Monitoreo con Prometheus y Grafana

El monitoreo de sistemas y aplicaciones es esencial para garantizar la confiabilidad y el rendimiento. En este documento se describira cómo configurar y utilizar Prometheus y Grafana en contenedores Docker para el monitoreo.

### Requisitos Previos

- Docker instalado en la máquina anfitriona.
- Acceso a los sistemas y aplicaciones que se desean monitorear.

### Configuración de Prometheus en Docker

1. **Descarga la imagen de Prometheus:**

   Utiliza el siguiente comando para descargar la imagen oficial de Prometheus desde Docker Hub:

   ```bash
   docker pull prom/prometheus
   ```

2. **Configura el archivo `prometheus.yml`:**

   Crea un archivo `prometheus.yml` que defina los objetivos de monitoreo y las reglas de alerta. Asegúrate de que las rutas a los objetivos sean las correctas en el archivo de configuración.

3. **Ejecuta Prometheus en Docker:**

   Ejecuta Prometheus en un contenedor Docker con el siguiente comando:

   ```bash
   docker run -d -p 9090:9090 -v /ruta/local/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
   ```

   Asegúrate de que `/ruta/local/prometheus.yml` sea la ruta local al archivo de configuración `prometheus.yml` que creaste.

### Configuración de Grafana en Docker

1. **Descarga la imagen de Grafana:**

   Descarga la imagen oficial de Grafana desde Docker Hub con el siguiente comando:

   ```bash
   docker pull grafana/grafana
   ```

2. **Ejecuta Grafana en Docker:**

   Ejecuta Grafana en un contenedor Docker con el siguiente comando:

   ```bash
   docker run -d -p 3000:3000 grafana/grafana
   ```

   Esto iniciará Grafana en el puerto 3000 dentro del contenedor.

3. **Configura Grafana:**

   Accede a la interfaz web de Grafana en `http://localhost:3000` (o la dirección IP de tu máquina anfitriona) y configura las credenciales de administrador. Luego, configura una fuente de datos para Prometheus en Grafana, utilizando la URL de Prometheus en Docker.

### Documentación

1. **Registro de Configuración:**

   Mantén un registro detallado de la configuración de Prometheus y Grafana en Docker, incluyendo versiones, cambios y ajustes realizados.

   #### Archivo docker-compose.yml

   ```
    version: "3.7"
    volumes:
    grafana-data:
    prometheus-data:

    services:
    grafana:
        image: grafana/grafana:8.0.6
        container_name: grafana
        restart: unless-stopped
        volumes:
        - grafana-data:/var/lib/grafana
        ports:
        - 3000:3000

    prometheus:
        image: prom/prometheus:v2.28.1
        container_name: prometheus
        restart: unless-stopped
        volumes:
        - ./prometheus.yml:/etc/prometheus/prometheus.yml
        - prometheus-data:/prometheus
        ports:
        - 9090:9090
        command:
        - '--config.file=/etc/prometheus/prometheus.yml'
        - '--storage.tsdb.path=/prometheus'
        - '--storage.tsdb.retention.time=1y'
        - '--web.enable-lifecycle'
   ```

   #### Archivo prometheus.yml

   ```
   global:
   scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
   evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.

   scrape_configs:
   - job_name: 'prometheus'
       static_configs:
       - targets: ['172.0.0.00:9090']

   - job_name: 'bd_test'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'amauta_test'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'maipi_test'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'maipi'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'delfin'
       static_configs:
       - targets: ['172.0.0.00:9100','172.0.0.00:9100']

   - job_name: 'recaudacion'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'conecta'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'websocket'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'matricula'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'amauta'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'cap-test'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'viceacad'
       static_configs:
       - targets: ['172.0.0.00:9100']

   - job_name: 'blackbox'
       metrics_path: /probe
       params:
       module: [http_2xx]  # Look for a HTTP 200 response.
       static_configs:
       - targets:
           - https://amauta.lamolina.edu.pe   # Target to probe with https.
           - https://maipi.lamolina.edu.pe
           - https://matricula.lamolina.edu.pe
           - https://conecta.lamolina.edu.pe
           - https://viceacad.lamolina.edu.pe
           - https://maipi-test.lamolina.edu.pe
           - https://amauta-test.lamolina.edu.pe
           - https://matricula-test.lamolina.edu.pe
       relabel_configs:
       - source_labels: [__address__]
           target_label: __param_target
       - source_labels: [__param_target]
           target_label: instance
       - target_label: __address__
           replacement: 172.0.0.00:9115  # The blackbox exporter's real hostname:port.
   ```

   #### Archivo blackbox.yml

   ```
   modules:
   http_2xx:
     prober: http
     http:
       preferred_ip_protocol: "ip4"
   ```

   #### Comandos para reiniciar los servicios

   ```
   docker restart blackbox
   docker restart prometheus
   docker restart grafana
   ```

# Monitoreo de servidores

a. Agregar monitoreo con Node Exporter

- Instalar docker en el servidor cliente
- Ejecutar el siguiente comando para descargar node exporter y ejecutarlo

  ```
  docker run -d -p 9100:9100 --name=node-exporter prom/node-exporter --web.listen-address="0.0.0.0:9100"
  ```

- En el servidor de monitoreo, en el archivo prometheus.yml agregar la configuracion dentro de scrape_configs agregando el nombre en job_name y la ip en targets indicando el puerto, en este caso 9100.

b. Monitoreo de paginas web

- Agregar la url de la pagina web a monitorear indicando si maneja el protocolo http o https en el archivo de prometheus.yml en la seccion targets.

### Alertas con Grafana

a. Alerta via Gmail

# Configuración de Alertas desde Grafana vía Gmail

## Requisitos Previos

1. **Grafana Instalado**: Asegúrate de tener Grafana instalado y configurado en tu servidor.

2. **Configuración de SMTP en Grafana**: Necesitas tener configurado el servidor SMTP en Grafana para enviar correos electrónicos a través de Gmail. Puedes acceder a esta configuración en la interfaz web de Grafana en la sección de "Configuration" > "Notification channels".

3. **Cuenta de Gmail**: Necesitarás una cuenta de Gmail para enviar correos electrónicos desde Grafana.
   Configuración de Gmail:
   Habilitar el Acceso a Aplicaciones Menos Seguras:
   Accede a la configuración de seguridad de tu cuenta de Gmail.
   Habilita la opción de "Acceso a aplicaciones menos seguras". Esto permitirá que Grafana envíe correos electrónicos a través de tu cuenta.

## Paso 1: Configuración de SMTP en Grafana

1. Inicia sesión en Grafana.

2. Navega a "Configuration" > "Notification channels".

3. Haz clic en "Add Channel" y selecciona "Email" como tipo de canal.

4. Completa los detalles del servidor SMTP:

   - **Type**: Elige "Email".
   - **Name**: Un nombre descriptivo para el canal.
   - **Address**: La dirección de correo electrónico desde la que se enviarán las alertas.
   - **SMTP Host**: `smtp.gmail.com`.
   - **SMTP Port**: `587` (puerto de Gmail para TLS).
   - **Username**: Tu dirección de correo electrónico de Gmail.
   - **Password/Secure fields**: La contraseña de tu cuenta de Gmail.

5. Haz clic en "Add" para guardar la configuración.

## Paso 2: Configuración de Alertas en Grafana

1. Abre o crea un tablero en Grafana.

2. Añade un panel que desees supervisar.

3. Haz clic en el panel y selecciona "Edit".

4. En la pestaña "Alert", haz clic en "Add Alert".

5. Configura las condiciones de la alerta según tus necesidades.

6. En la sección "Send to", elige el canal de notificación que configuraste previamente.

7. Define las opciones de notificación, como el intervalo de repetición y la duración de la condición de alerta.

8. Haz clic en "Apply" para guardar la configuración del panel.

## Paso 3: Prueba de Alertas

1. Haz clic en "Apply" para guardar los cambios en el tablero.

2. Simula una condición de alerta para verificar si las notificaciones se envían correctamente.

3. Revisa la cuenta de correo electrónico de Gmail para confirmar que has recibido la alerta.

¡Listo! Ahora deberías tener configuradas las alertas desde Grafana vía Gmail. Asegúrate de monitorear regularmente las alertas y ajustar la configuración según sea necesario.

b. Alertas via Slack

# Configuración de Alertas desde Grafana vía Slack

## Requisitos Previos

1. **Grafana Instalado**: Asegúrate de tener Grafana instalado y configurado en tu servidor.

2. **Cuenta de Slack**: Necesitarás una cuenta en Slack y permisos para crear integraciones.

3. **Canal en Slack**: Crea un canal en Slack donde recibirás las alertas.

## Paso 1: Configuración de Webhook en Slack

1. Inicia sesión en tu cuenta de Slack.

2. Dirígete al canal en el que deseas recibir las alertas.

3. Haz clic en "Configuración del canal" > "Configuración de aplicaciones" > "Agregar una aplicación".

4. Busca "Incoming Webhooks" y selecciona la aplicación.

5. Activa los Webhooks para el canal seleccionado y copia el Webhook URL generado.

## Paso 2: Configuración de Notificaciones en Grafana

1. Inicia sesión en Grafana.

2. Navega a "Configuration" > "Notification channels".

3. Haz clic en "Add Channel" y selecciona "Slack" como tipo de canal.

4. Completa los detalles del canal:

   - **Type**: Elige "Slack".
   - **Name**: Un nombre descriptivo para el canal.
   - **Url**: Pega el Webhook URL que copiaste anteriormente.
   - **Mention**: (Opcional) Puedes mencionar a usuarios o canales específicos en Slack.

5. Haz clic en "Add" para guardar la configuración.

## Paso 3: Configuración de Alertas en Grafana

1. Abre o crea un tablero en Grafana.

2. Añade un panel que desees supervisar.

3. Haz clic en el panel y selecciona "Edit".

4. En la pestaña "Alert", haz clic en "Add Alert".

5. Configura las condiciones de la alerta según tus necesidades.

6. En la sección "Send to", elige el canal de notificación de Slack que configuraste previamente.

7. Define las opciones de notificación, como el intervalo de repetición y la duración de la condición de alerta.

8. Haz clic en "Apply" para guardar la configuración del panel.

## Paso 4: Prueba de Alertas

1. Haz clic en "Apply" para guardar los cambios en el tablero.

2. Simula una condición de alerta para verificar si las notificaciones se envían correctamente.

3. Verifica el canal de Slack para confirmar que has recibido la alerta.

¡Enhorabuena! Ahora deberías tener configuradas las alertas desde Grafana vía Slack. Asegúrate de monitorear regularmente las alertas y ajustar la configuración según sea necesario.

### Dockerizar aplicacion de amauta

```
FROM openjdk:8-jdk-alpine

WORKDIR /app
COPY target/amauta.jar app.jar
RUN apk add --update redis
EXPOSE 6379
EXPOSE 9000
CMD redis-server & java -jar app.jar
```

```
---------Crear una imagen y luego enviarlo a otro servidor
docker build -t amauta-docker3 .
docker save amauta-docker3 > /home/informatica/Descargas/dockeramauta.tar
scp /home/informatica/Descargas/dockeramauta.tar admin@172.16.0.178:/home/admin

---------CRear una imagen y correrlo
docker build -t amauta-docker3 .
docker run -p 9000:9000 -p 6379:6379 amauta-docker3
docker run -d -p 9000:9000 -p 6379:6379 amauta-docker6

```

# Mejoras en el sistema de monitoreo

a. Inicio automatico de los contenedores de monitoreo en los servidores

En el archivo start_monitor.sh agregar el siguiente comando:

```
docker start node-exporter
```

Y luego en crontab, agregar la tarea para que al reiniciar el servidor se ejecute el archivo start_monitor.sh

```
@reboot /lost+found/start_monitor.sh
```

Al realizar esta configuracion se podra preveer ante cualquier caida del servidor. Y asi al prender el servidor el contenedor de monitoreo se iniciara.
