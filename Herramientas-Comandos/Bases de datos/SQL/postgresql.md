#postgresql

-----

PostgreSQL es un sistema de gestión de bases de datos relacional de código abierto, potente y extensible, que permite almacenar y consultar datos de forma segura y eficiente. Es muy usado para aplicaciones web y empresariales.
Corre por el puerto 5432
# Como conectarnos

```shell
psql -U Usuario -h Ip -p Puerto
Ejemplo: psql -U christine -h localhost -p 1234
```
*Parametros:*
`-U` Para poner el usuario
`-h` Para poner la ip
`-p` El puerto

----
# Comandos
Una vez dentro de la base de datos, listamos la flag
```posgresql
\l                   |para ver las bases de datos
\c secrets                  |secrets para usar la base de datos
\dt                   |para ver las tablas y columnas
SELECT * FROM flag;   | para listar todo el contenido de la tabla flag
```

![[Funnel10.png]]
