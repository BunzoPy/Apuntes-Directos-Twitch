Nos sirve para ver donde esta ubicado el contenedor dentro de la maquina host

```
mount | grep Usuario
Ejemplo: mount | grep augustus
```


**`/dev/sda1`**: es la partición del disco duro que está montada.
**`/home/augustus`**: es el punto de montaje, es decir, dónde en el sistema de archivos se ha montado esa partición.
**`type ext4`**: el sistema de archivos que usa esta partición es **ext4**.

**`(rw,relatime,errors=remount-ro)`**: opciones de montaje:
	**`rw`**: se puede leer y escribir (read/write).
	**`relatime`**: optimiza las escrituras al disco actualizando los tiempos de acceso de forma más eficiente.
	**`errors=remount-ro`**: si hay errores, el sistema monta la partición como de solo lectura (para evitar daños).