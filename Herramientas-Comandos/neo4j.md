#neo4j

-----

Es una **base de datos orientada a grafos**, diseñada para almacenar y consultar información que se representa como nodos y relaciones, en lugar de tablas como en las bases de datos relacionales tradicionales.

---
# Instalarlo

```shell
apt install neo4j
```


------
# Ejecucion

Para iniciar la base de datos usamos el comando 

`neo4j console`

![[Administrator16.png]]
Y nos dice si queremos entrar a la base de datos usamos localhost:7474 por si queremos hacer algun tipo de configuracion
Y si nos queremos conectar para utilizarla y cargar datos es en localhost:7687

![[Administrator17.png]]

---
# Primera conexion

Si nos vamos conectar por primera vez entrar a localhost:7474 para cambiar la contraseña que viene por defecto y recordar estas credenciales

------
# Como borrar base de datos

Usando este [articulo](https://thecodebuzz-com.translate.goog/neo4j-delete-reset-databases-nodes-relationship-with-examples/?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es)
En la ruta */etc/neo4j* voy a usar el comando ``rm -rf data/*``

y  despues volvi a iniciar con neo4j console
me meti a localhost:7474 me logie con neo4j:neo4j 
	cambie la contraseña por hola123 y listo db nueva y limpita


------
# Mi contraseña es hola123