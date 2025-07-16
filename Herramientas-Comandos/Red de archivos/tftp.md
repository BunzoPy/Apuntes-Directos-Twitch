**TFTP** (Trivial File Transfer Protocol) es un protocolo simple para transferir archivos entre dispositivos en una red.  
Usa el puerto **UDP 69** y **no requiere autenticación**, por lo que es rápido pero **inseguro**.  
Se usa comúnmente en entornos cerrados, como routers, PXE boot o dispositivos embebidos.

Es mejor usar FTP, no se puede listar directorios ni su contenido

----------

#### Comando para conectarse:
```shell
tftp Ip Puerto
Ejemplo: tftp 10.129.250.6 69
```

### Comandos:
`put` Es para subir archivos                |  put NombreDelArchivo | Ejemplo put backup.zip
`get` Descarga archivos                     |  get NombreDelArchivo | Ejemplo get backup.zip
``