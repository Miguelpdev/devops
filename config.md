# Pasos para desplegar la aplicacion Maipi-Test

- Configurar Dockerfile

```
FROM openjdk:8-jdk-alpine
WORKDIR /app
COPY target/maipi.jar app.jar
RUN apk add --update redis
EXPOSE 6379
EXPOSE 9002
ENV USERDB=user
ENV PASSWORD=password
CMD redis-server & java -jar app.jar --spring.profiles.active=test
```

### Construir la imagen

Ubicarse en la direccion del Dockerfile

`docker build -t maipi-test .`

Correr la aplicacion
