# Jenkins

Para instalar Jenkins utilizando Docker en CentOS 7, puedes seguir estos pasos. Asegúrate de que Docker esté instalado en tu máquina CentOS antes de continuar.

### Paso 1: Instalar Docker en CentOS 7

```bash
# Instalar paquetes necesarios
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# Configurar el repositorio estable de Docker
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Instalar Docker
sudo yum install -y docker-ce docker-ce-cli containerd.io

# Iniciar y habilitar Docker
sudo systemctl start docker
sudo systemctl enable docker

# Agregar tu usuario al grupo docker (opcional, evita usar sudo con comandos de docker)
sudo usermod -aG docker $(whoami)

# Cerrar sesión y volver a iniciar sesión para aplicar los cambios de grupo
exit
```

### Paso 2: Descargar y Ejecutar la Imagen de Docker de Jenkins

```bash
# Descargar la imagen de Docker de Jenkins
docker pull jenkins/jenkins:lts

# Crear un directorio en la máquina anfitriona para persistir los datos de Jenkins
sudo mkdir -p /var/jenkins_home
sudo chown 1000:1000 /var/jenkins_home  # Asegurar que el directorio sea propiedad del usuario de Jenkins en el contenedor

# Ejecutar Jenkins como un contenedor de Docker
docker run -d -p 8080:8080 -p 50000:50000 -v /var/jenkins_home:/var/jenkins_home --name jenkins jenkins/jenkins:lts
```

Explicación del comando `docker run`:

- `-d`: Ejecutar el contenedor en segundo plano (modo desvinculado).
- `-p 8080:8080`: Mapear el puerto 8080 en la máquina anfitriona al puerto 8080 en el contenedor (interfaz web de Jenkins).
- `-p 50000:50000`: Mapear el puerto 50000 en la máquina anfitriona al puerto 50000 en el contenedor (comunicación del agente de Jenkins).
- `-v /var/jenkins_home:/var/jenkins_home`: Montar el directorio de la máquina anfitriona `/var/jenkins_home` en el directorio del contenedor `/var/jenkins_home` para persistir los datos de Jenkins.
- `--name jenkins`: Asignar el nombre "jenkins" al contenedor en ejecución.
- `jenkins/jenkins:lts`: Utilizar la versión LTS (Soporte a Largo Plazo) de la imagen de Docker de Jenkins.

### Paso 3: Acceder a la Interfaz Web de Jenkins

Una vez que el contenedor esté en ejecución, puedes acceder a la interfaz web de Jenkins navegando a `http://tu_direccion_ip_del_servidor:8080` en tu navegador web.

Obtén la contraseña inicial del administrador de Jenkins ejecutando:

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copia la contraseña y pégala en la interfaz web de Jenkins para desbloquear y configurar Jenkins.

Usa la imagen oficial de Jenkins basada en Alpine

```
FROM jenkins/jenkins:lts-alpine

#Cambia al usuario root para instalar herramientas adicionales

USER root

#Instala OpenJDK 11 (puedes cambiar la versión si es necesario)

RUN apk update && \
 apk add openjdk11

#Instala Maven (puedes cambiar la versión si es necesario)

RUN apk add maven

#Cambia nuevamente al usuario jenkins

USER jenkins
```

- Eliminar datos de imagen si no se vuelven a cargar configuraciones ya que se quedo en el cache de la data.

```
docker volume ls
docker volume rm jenkins-data
```

- Comando para actuar como usuario root
  `sudo -i`

- Comando para conectarse por bash al contenedor docker

```
sudo docker exec -it jenkins /bin/bash
```

## Docker compose

```
services:
    jenkins:
       build: .
       restart: on-failure
       privileged: true
       container_name: jenkins
       user: root
       volumes:
        - jenkins-home:/var/jenkins_home
        - ./jenkins:/var/jenkins_home/.ssh

       ports:
        - 8080:8080
        - 50000:50000

volumes:
    jenkins-home:
```

## Dockerfile

```
FROM jenkins/jenkins:lts-alpine
# # # Cambia al usuario root para instalar herramientas adicionales
USER root
RUN apk update && \
  apk add openjdk11
RUN apk add maven
USER jenkins
```

## Jenkinsfile-devel

```
pipeline {
    agent any

    parameters {
        choice(name: 'SOURCE_KIND', choices: ['branch', 'commit', 'tag'], description: 'Origen del código del repositorio git.')
        string(name: 'SOURCE', defaultValue: 'master', description: 'Branch, Commit Id o Tag del código del repositorio git.')
        string(name: 'PROFILE', defaultValue: 'test', description: 'Spring Profile for deployment.')
        string(name: 'SERVER', defaultValue: 'otros-test.lamolina.edu.pe', description: 'Dirección IP del servidor remoto.')
        string(name: 'PORT', defaultValue: '443', description: 'Puerto de servidor en que se ejecuta el pipeline.')
        string(name: 'REMOTE_DIR', defaultValue: '/lost+found', description: 'Directorio remoto para la transferencia de archivos.')
        string(name: 'USERNAME', defaultValue: 'root', description: 'Nombre de usuario SSH para la conexión al servidor remoto.')
    }

    environment {
        DELFIN_TEST = credentials('delfin-test')
    }

    tools {
        jdk 'java11'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    if (params.SOURCE_KIND == 'branch') {
                        checkout scmGit(branches: [[name: '*/' + params.SOURCE]])
                    } else {
                        checkout scmGit(branches: [[name: params.SOURCE]])
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Transfer Files') {
            steps {
                script {
                    def remoteCommand = "scp target/*.jar ${params.USERNAME}@${params.SERVER}:${params.REMOTE_DIR}/"
                    sh remoteCommand
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def remoteCommand = """
                        ssh root@otros-test.lamolina.edu.pe '
                            fuser -n tcp -k ${PORT}
                            export USERDB=${DELFIN_TEST_USR}
                            export PASSWORD='${DELFIN_TEST_PSW}'
                            nohup java -jar ${params.REMOTE_DIR}/*.jar --spring.profiles.active=${params.PROFILE} > /dev/null 2>&1 &
                            wait-for-it -h localhost -p ${PORT} -t 60
                        '
                    """
                    sh remoteCommand
                }
            }
             post {
                success {
                    slackSend (
                        color: 'good',
                        message: "¡Despliegue exitoso en ${params.SERVER}!",
                        tokenCredentialId: 'jenkins-id'
                    )
                }
                failure {
                    slackSend (
                        color: 'danger',
                        message: "¡Error durante el despliegue en ${params.SERVER}!",
                        tokenCredentialId: 'jenkins-id'
                    )
                }
            }
        }
    }
}
```
