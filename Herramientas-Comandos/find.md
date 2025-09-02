Se usa para **buscar archivos y directorios** en el sistema de archivos de Linux.  
Permite filtrar por nombre, permisos, tamaño, fecha, tipo, etc.


----
# Parametro: -perm | Busqueda por permisos [[SUID]]

Podemos buscar por el permiso que querramos, pero lo mas normal es que busquemos por [[SUID]]
```
Ejemplo: find / -perm -4000 2>/dev/null            /Este va a buscar archivos qeu tengan el SUID activado
Ejemplo: find / -perm 4000 2>/dev/null             /Aca va a buscar archivos con el permiso 4000 exacto, que seria solamente el SUID y nada mas
```
*Parametros*
`/`Significa que esta buscando desde la raiz del sistema
`-perm` Se pone el permiso
*2>/dev/null* No nos muestra los errores y los envia a la carpeta /dev/null

------
# Parametro: -group
Es para buscar archivos que pertenezcan a x grupo

```
find / -group Grupo 2>/dev/null
Ejemplo: find / -group bugtraker 2>/dev/null
```

`/`Significa que esta buscando desde la raiz del sistema
`-group` Se pone el grupo
*2>/dev/null* No nos muestra los errores y los envia a la carpeta /dev/null

------
# Parametro: -name
Busca archivos por nombre

```
find / -group NombreDelArchivo 2>/dev/null
Ejemplo para buscar la flag de intrusion: find / -name flag.txt 2>/dev/null
Ejemplo para buscar la flag de escalada de privilegios: find / -name root.txt 2>/dev/null
```

`/`Significa que esta buscando desde la raiz del sistema
`-name` Nombre del archivo
	Si pongo "\*springs*" Va a buscar resultados que empiezen, terminen o tengan la palabra springs
*2>/dev/null* No nos muestra los errores y los envia a la carpeta /dev/null