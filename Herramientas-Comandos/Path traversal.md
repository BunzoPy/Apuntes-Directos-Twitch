**Path Traversal** es una técnica que aprovecha rutas mal filtradas para moverse por el sistema de archivos.  
Permite a un atacante salir del directorio restringido y acceder a archivos protegidos.

Ejemplo: ../../../../../../../../

![[pathtraversal.png]]
En este caso estabamos en /home/bunzo/Desktop/Oopsie y al usar el cd ../../../../../ nos mando a carpeta raiz del sistema operativo