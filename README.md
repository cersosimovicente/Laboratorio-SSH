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



