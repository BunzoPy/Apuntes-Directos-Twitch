**¿Qué es Redis?**  
Redis es una base de datos NoSQL en memoria, tipo clave-valor, muy rápida y de código abierto.

**¿Para qué sirve?**  
Se usa como caché, almacenamiento temporal, colas de tareas, contador de datos, y sistema de mensajería en tiempo real (pub/sub).

Normalmente corre en el puerto 6379

## Como conectarse a la base de datos
```shell
recis-cli -h Ip -p Puerto
Ejemplo: redis-cli -h 10.129.48.19 -p 6379
```

### Como dumpear datos
Con el comando `info keyspace` vimos que base de datos estaban
	![[Redeemer3.png]]
Seleccionamos la base de datos 0 con el comando ``select 0``, despues visualizamos todas las keys con `keys *` y con `get flag` sacamos la flag de la maquina

![[Redeemer4.png]]