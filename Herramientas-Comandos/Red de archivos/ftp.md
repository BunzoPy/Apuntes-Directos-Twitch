#ftp

**FTP (File Transfer Protocol)** es un protocolo de red que sirve para **transferir archivos entre un cliente y un servidor** en una red, como Internet.
Normalmente corre por el puerto 21
Esta el servicio de SFTP(Secure File Transfer Protocol) la informacion va cifrada

*Codigos de estado mas usuales:*
- **200** – Comando exitoso.
- **220** – Servicio listo para nuevos usuarios.
- **221** – Cerrando la conexión.
- **230** – Usuario ha iniciado sesión correctamente.
- **331** – Usuario OK, pero necesita contraseña.
- **530** – No se pudo iniciar sesión.

##### Comandos de ayuda
```shell
ftp -h
ftp       | y una vez estamos en la consola interactiva ponemos help
```

#### Comando para conectarse:
```shell
ftp Ip
Ejemplo: ftp 10.129.250.6
```
*Parametros*
`-p` Es para poner el puerto

### Comandos:
`get` Se usa para descargar archivos                |  get NombreDelArchivo | Ejemplo get backup.zip
`ls` Lista el contenido del directorio
`cd` Sirve para movernos por los directorios

-----

# Vulnerabilidad con usuario anonymous

### Enumeracion
![[ftp2.png]]
En el escaneo que hacemos con [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]] nos aparece la vulnerabilidad

### Explotacion
Poniendo de usuario anonymous podemos logearnos al servicio ftp sin proporcionar ninguna contraseña
![[ftp1.png]]