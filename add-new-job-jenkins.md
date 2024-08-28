# Jenkins agregar nueva tarea

## Jenkins (maipi-test)

### **1. Generar un Par de Claves SSH en `maipi-test` (si aún no tienes uno)**

Si ya tienes un par de claves SSH que Jenkins usa para conectarse a otros servidores, puedes reutilizarlas. Si no, sigue estos pasos para generar una nueva:

#### **a. Accede al Servidor `maipi-test`**

Abre una terminal y conéctate al servidor `maipi-test` donde está corriendo Jenkins.

#### **b. Genera un Nuevo Par de Claves SSH**

Ejecuta el siguiente comando para generar una nueva clave SSH:

```bash
ssh-keygen -t rsa -b 4096 -C "jenkins@maipi-test"
```

o

```
ssh-keygen -t rsa -b 2048
```

- **Ubicación**: Por defecto, se guardará en `~/.ssh/id_rsa`. Puedes presionar **Enter** para aceptar la ubicación predeterminada.
- **Passphrase**: Puedes dejarla vacía presionando **Enter** o ingresar una passphrase para mayor seguridad. Si usas una passphrase, necesitarás ingresarla al configurar las credenciales en Jenkins.

### **2. Copiar la Clave Pública a `amauta-test`**

Para que Jenkins pueda autenticar usando la clave privada, la clave pública correspondiente debe estar en el servidor `amauta-test`.

#### **a. Usar `ssh-copy-id`**

Desde `maipi-test`, ejecuta:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub usuario@amauta-test.lamolina.edu.pe
```

- **Reemplaza** `usuario` con el nombre de usuario SSH que usarás para conectarte a `amauta-test`.

**IMPORTANTE** ▶️

En este caso de nuestra empresa la clave publica fue cambiada, asi que copia la clave desde otro servidor que ya tenga acceso a `maipi-test` como por ejemplo `amauta` desde el archivo `~/.ssh/authorized_keys` al servidor que quieras agregar a la misma direccion y archivo

#### **b. Alternativa Manual**

Si `ssh-copy-id` no está disponible, puedes copiar manualmente la clave pública:

1. **En `maipi-test`**:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

   Copia el contenido que aparece.

2. **En `amauta-test`**:
   - Conéctate al servidor `amauta-test`.
   - Abre (o crea) el archivo `~/.ssh/authorized_keys` del usuario correspondiente.
   - Pega la clave pública copiada en una nueva línea.
   - Guarda y cierra el archivo.

### **3. Obtener la Clave Privada de `maipi-test`**

La clave privada se encuentra en `maipi-test` y es la que Jenkins utilizará para autenticarse en `amauta-test`.

#### **a. Abrir la Clave Privada**

En `maipi-test`, abre el archivo de la clave privada con un editor de texto seguro:

```bash
cat ~/.ssh/id_rsa
```

O usa un editor como `nano` o `vim`:

```bash
vim ~/.ssh/id_rsa
```

#### **b. Copiar el Contenido de la Clave Privada**

Copia todo el contenido del archivo, incluyendo las líneas que dicen `-----BEGIN OPENSSH PRIVATE KEY-----` y `-----END OPENSSH PRIVATE KEY-----`.

### **4. Configurar la Credencial SSH en Jenkins**

Ahora que tienes la clave privada, configúrala en Jenkins.

#### **a. Accede a las Credenciales en Jenkins**

1. Ve a `Jenkins > Manage Jenkins > Manage Credentials`.
2. Selecciona el almacén de credenciales adecuado, generalmente **(global)**.

#### **b. Agregar una Nueva Credencial SSH**

1. Haz clic en **Add Credentials**.
2. En el campo **Kind**, selecciona **SSH Username with private key**.

#### **c. Configura la Nueva Credencial**

- **Username**: Ingresa el nombre de usuario SSH que usarás para conectarte a `amanta-test`.
- **Private Key**: Selecciona **Enter directly** y pega el contenido de la clave privada que copiaste de `maipi-test` (`~/.ssh/id_rsa`).
- **Passphrase**: Si tu clave privada tiene una passphrase, ingrésala aquí.
- **ID** o **Description**: Asigna un nombre identificativo a esta credencial para reconocerla fácilmente.
- Haz clic en **OK** para guardar la credencial.

### **5. Configurar "Publish Over SSH" para Usar la Nueva Credencial**

#### **a. Ve a la Configuración de "Publish Over SSH"**

1. Ve a `Jenkins > Manage Jenkins > Configure System`.
2. Busca la sección **Publish over SSH**.

#### **b. Configura el Servidor `amanta-test`**

1. **Add** un nuevo servidor si aún no lo has hecho.
2. **Name**: `amauta-test`.
3. **Hostname**: Dirección IP o nombre de host de `amauta-test`.
4. **Username**: Usuario SSH que configuraste en las credenciales.
5. **Remote Directory**: (Opcional) Directorio remoto donde se ejecutarán los comandos.
6. **Test Configuration**: Usa esta opción para verificar que Jenkins pueda conectarse correctamente al servidor `amanta-test`.

#### **c. Guardar la Configuración**

Haz clic en **Save** para guardar los cambios.

### **6. Configurar el Job en Jenkins**

1. Crea o configura el job que necesitas en Jenkins.
2. En la configuración del job, agrega una nueva acción **Pipeline**.
3. **Selecciona Pipeline**: Elige `pipeline script from scm`.
4. **SCM**: Elige `git` y agregue su repositorio y ss credenciales .
5. **Script Path**: Digite la direccion de su JenkisFile en su repositorio en este caso es `Jenkinsfile-test`.
6. Guarda la configuración del job.

### **7. Probar la Conexión**

1. **Guardar y Ejecutar**: Guarda la configuración del job y ejecútalo para verificar que todo funcione correctamente.
2. **Revisar Logs**: Si hay algún problema, revisa los logs de Jenkins (`Jenkins > Manage Jenkins > System Log`) para obtener más detalles sobre los errores.

### **Resumen**

- **Servidor de Origen**: `maipi-test` (donde está corriendo Jenkins).
- **Servidor de Destino**: `amauta-test` (donde se ejecutará la aplicación).
- **Clave Privada**: Debe generarse o estar presente en `maipi-test` y copiarse en las credenciales de Jenkins.
- **Clave Pública**: Debe estar añadida en `~/.ssh/authorized_keys` del usuario en `amauta-test`.

### **Notas de Seguridad**

- **Protege tu Clave Privada**: Asegúrate de que la clave privada se mantenga segura y no se comparta con otros.
- **Permisos de Archivos**: Verifica que los permisos de los archivos de claves sean correctos (`chmod 700 ~/.ssh` y `chmod 600 ~/.ssh/authorized_keys` en `amanta-test`).

Si sigues estos pasos, deberías poder configurar correctamente la conexión SSH entre Jenkins en `maipi-test` y `amanta-test`. Si encuentras algún error específico, no dudes en proporcionarlo para que pueda ayudarte a solucionarlo.
