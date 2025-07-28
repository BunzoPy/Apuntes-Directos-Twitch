#mysql 

---------

SQL (Structured Query Language) es un lenguaje diseñado para gestionar y manipular bases de datos relacionales.  
Permite consultar, insertar, actualizar y eliminar datos usando comandos estructurados.
Normalmente corre por el puerto 3306
*Terminar siempre las consultas con ;*

---------
# Como conectarse
#### Conectarse con el usuario root sin otortar contraseña

```shell
mysql -h Ip -P Puerto -u root
Ejemplo: mysql -h 10.129.68.124 -P 3306 -u root
```
*Parametros:*
`-h` Ip
`-P` Puerto
`-u` Usuario
`-p` Contraseña

![[mysql1.png]]

--------
# Como dumpear datos

```
show databases;  
use htb;
show tables;
select * from config;
```

`show databases;` Es para mostrar todas la bases de datos
`use htb;` Es para usar en este caso la base de datos llamada htb
`show tables;` Es para mostrar todas las tablas
`select * from config;` Muestra todo el contenido de la tabla config

Usamos la base de datos htb y despues dumpeamos los datos de la tabla config
![[Sequel5.png]]