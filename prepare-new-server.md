# New Server

### Install

#### Instalar vim

```
sudo yum update
sudo yum install vim
```

#### Instalar java 1.8

```
sudo yum install java-1.8.0-openjdk-devel
```

sudo vim /etc/profile

#### Agregar en la parte final del archivo

```export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
export PATH=$PATH:$JAVA_HOME/bin
Para guardar los cambios
source /etc/profile
```

#### Instalación de Maven

```
sudo yum install maven
mvn --version
```

#### Instalar git

```
sudo yum install git
git --version
```

#### Crear el archivo log para la aplicación maipi y matricula

```
/var/log/jenkins/lamolina-intranet.log MAIPI
/var/log/jenkins/lamolina-matricula.log MATRICULA
```

#### Dar acceso a otro usuarios para que edite el log probar

```
sudo chmod ugo+rw lamolina-intranet.log
```

#### Instalar el redis

```
sudo yum install redis
sudo systemctl start redis
sudo systemctl status redis
sudo systemctl enable redis
```

## Deployar la aplicación

#### matricula.jar manualmente

##### cuando el usuario y pass no están en la aplicación

```
export USERDB=test
export PASSWORD=password
nohup java -jar /lost+found/matricula.jar --spring.profiles.active=test 2 &
```

#### Sincronizar la hora

```
sudo yum install chrony
sudo systemctl start chronyd
sudo systemctl enable chronyd
chronyc tracking
```

#### Instalar fuser

```
sudo yum install -y psmisc
fuser --version
```

#### Instalar wait-for-it

```
cd /usr/local/bin
sudo curl -o wait-for-it.sh https://raw.githubusercontent.com/vishnubob/wait-for-it/master/wait-for-it.sh

```

```
sudo chmod +x /usr/local/bin/wait-for-it.sh
```

```
sudo mv /usr/local/bin/wait-for-it.sh /usr/local/bin/wait-for-it
```

```
wait-for-it --help
```
