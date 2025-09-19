#sed

------

Es una herramienta de línea de comandos en Unix/Linux que **lee texto, lo transforma según reglas y envía el resultado a la salida estándar**, todo sin necesidad de abrir un editor de archivos.

-------
# Comando

```
Ejemplo
cat texto.txt| grep -i "sidtypeuser" | sed 's/.*\\//' | sed 's/ .*//' > users.txt
```
Aca se usan las regex para hacer las sustituciones, y estoy usando el cat para abrir el archivo y el grep para filtrar por la palabra que quiero