#rsync 

------
Es una herramienta de línea de comandos en Linux usada para **copiar, sincronizar o respaldar archivos y directorios** entre computadoras, ya sea localmente o por red.
Normalmente corre por el puerto 873

----------
# Posible vulnerabilidad en credenciales
NONE: No pasamos ningun tipo de credencial para el logeo y puede ser que nos este permitiendo entrar para listar archivos

------
# Como conectarse y descargar archivos
```shell
rsync rsync://Ip:Puerto

Ejemplo: 
rsync rsync://10.129.187.112:873
rsync rsync://10.129.187.112:873/public
rsync rsync://10.129.187.112:873/public/flag.txt .
```

El segundo *rsync* que ponemos es de la url a la que queremos ingresar

Primero nos conectamos a la maquina, luego entramos a la carpeta que nos parezca relevante, y al final ponemos el nombre del archivo que queremos descargar seguido de un espacio y . para que se descarge en el directorio actual de trabajo


![[Synced5.png]]

-----------

# Notas

Cuando usás `--list-only` (o solo `list-only` en este caso), `rsync` muestra los archivos y carpetas disponibles **sin descargarlos**, útil para explorar lo que hay en un recurso remoto.