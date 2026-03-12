# Laboratorio-SSH
# Acceso remoto con SSH (Escenario Proxmox + Windows)
## Objetivos

Al finalizar este laboratorio el estudiante será capaz de:

- Comprender el funcionamiento del protocolo **SSH**.
- Conectarse a un servidor Linux de forma remota.
- Administrar un servidor desde una terminal.
- Implementar autenticación segura mediante claves SSH.
- Transferir archivos mediante SCP.
--- 
El servicio utilizado será **OpenSSH** ejecutándose en un contenedor LXC.

# Escenario del laboratorio
Servidor Linux en contenedor LXC dentro de Proxmox y cliente Windows usando PowerShell.
                RED LAN
        192.168.1.0 /24
                 
        ┌──────────────────┐
        │   PC del alumno  │
        │                  │
        │ Windows          │
        │ PowerShell SSH   │
        │ 192.168.128.x    │
        └─────────┬────────┘
                  │
                  │ conexión SSH
                  │ puerto 22
                  │
        ┌─────────▼─────────┐
        │  Servidor Linux   │
        │  Contenedor LXC   │
        │  en Proxmox       │
        │                   │
        │ OpenSSH Server    │
        │ 192.168.128.x     │
        └───────────────────┘

# Parte 1 — Preparación del servidor SSH
```bash
#Ingresar al contenedor desde el host de Proxmox.ss -tlnp | grep ssh
#Actualizar el sistema.
apt update
#Instalar el servidor SSH.
apt install openssh-server
#Verificar el estado del servicio.
systemctl status ssh
#Si no está activo:
systemctl start ssh
systemctl enable ssh
#Verificar puerto de escucha.
ss -tlnp | grep ssh
```
# Parte 2 — Crear usuario para acceso remoto
```bash
#Crear un usuario para el laboratorio.
adduser alumno (aca va tu nombre)
#Opcional: permitir privilegios sudo.
usermod -aG sudo alumno
```
# ETAPA 1 — Conexión SSH con usuario y contraseña
Desde la computadora del alumno (fisica o virtual) abrir PowerShell.
Conectarse al servidor.
```bash
ssh alumno@192.168.128.x
```
La primera vez aparecerá el mensaje de autenticidad del host.

Aceptar con:

`yes`

Luego ingresar la contraseña del usuario.

Si la conexión es exitosa aparecerá algo similar a:

`alumno@servidor:~$`

Salir de la conexión.

`exit`

# Ejercicio práctico
Una vez conectados ejecutar los siguientes comandos.
```bash
whoami
hostname
ip a
```
Crear un archivo de prueba.

`touch prueba.txt`

Cerrar la sesión.

`exit`

# ETAPA 2 — Autenticación mediante claves SSH
SSH permite autenticarse mediante criptografía de clave pública.
Funcionamiento:
- la `clave pública` se guarda en el servidor
- la `clave privada` se guarda en el cliente

Esto permite conectarse sin contraseña.

# Parte 3 — Generar claves SSH en Windows
```bash
ssh-keygen
```
Aceptar la ruta por defecto.


`C:\Users\usuario\.ssh\id_rsa` 


Se generarán dos archivos:
```bash
id_rsa
id_rsa.pub
```
# Parte 4 — Copiar la clave al servidor
Mostrar la clave pública.
```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub
```
Copiar el contenido.

## En el servidor Linux
Crear el directorio `.ssh`.
```bash
mkdir /home/alumno/.ssh
```
Crear el archivo de claves autorizadas.
```bash
nano /home/alumno/.ssh/authorized_keys
```
Pegar la clave pública dentro del archivo.

## Configurar permisos

SSH requiere permisos estrictos.

```bash
chown -R alumno:alumno /home/alumno/.ssh
chmod 700 /home/alumno/.ssh
chmod 600 /home/alumno/.ssh/authorized_keys
```
# Parte 5 — Probar acceso sin contraseña
Desde Windows ejecutar nuevamente:
```powershell
ssh alumno@192.168.128.x
```
Resultado esperado:

✔ conexión directa

✔ no solicita contraseña

# Parte 6 — Deshabilitar autenticación por contraseña
Editar configuración del servidor. ***antes realiza una copia de seguridad del archivo***
```bash
nano /etc/ssh/sshd_config
#Configurar:
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
#Reiniciar el servicio.
systemctl restart ssh
```
# Parte 7 — Transferencia de archivos con SCP
```powershell
#Copiar archivo al servidor.
scp archivo.txt alumno@192.168.128.x:/home/alumno
#Copiar archivo desde el servidor.
scp alumno@192.168.128.x:/home/alumno/prueba.txt .
```
# Entregable
Se debera entregar la salidas en formato Markdown
- conexión SSH inicial
- generación de claves
- archivo authorized_keys
- conexión sin contraseña
- transferencia de archivos con SCP

## Preguntas de reflexión
1. ¿Qué ventajas tiene usar claves SSH en lugar de contraseñas?
2. ¿Por qué SSH exige permisos estrictos en el directorio .ssh?
3. ¿Qué ocurre si la clave privada es comprometida?
4. ¿Qué diferencia existe entre SSH y Telnet?







