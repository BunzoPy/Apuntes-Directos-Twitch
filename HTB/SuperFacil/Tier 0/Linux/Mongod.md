#easy #linux #ping #nmap #mongodb

----
# Guided Mode

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

7)¿Cuál es el comando utilizado para volcar el contenido de todos los documentos de la colección denominada flag en un formato fácil de leer?
	db.flag.find()

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

![[Mongod1.png]]

![[Mongod2.png]]
*TTL:* Maquina Linux
*Puertos:*
	`22`[[ssh]]
	`27017` [[mongodb]]

-----
# Intrusion por [[mongodb]]

Descargamos el binario con [[curl]] para ejecutar [[mongodb]] por que no esta en la maquina. Despues con [[tar]] lo vamos a descomprimir, con [[cd]] entramos en la carpeta. Y con el comando final ejecutamos el binario entrando a la base de datos
```shell
curl -O https://downloads.mongodb.com/compass/mongosh-2.3.2-linux-x64.tgz
tar xf mongosh-2.3.2-linux-x64.tgz
cd mongosh-2.3.2-linux-x64/bin
./mongosh mongodb://10.129.228.30:27017
```


![[Mongod3.png]]

Ya estamos adentro asi que vamos a listar las bases de datos con `show dbs` Luego entramos a la base de datos *sensitive_information* con el comando `use sensitive_information`. Ahora con `show colections` vamos a listar las tablas. Y finalmente dumpeamos los datos de la tabla *flag* con `db.flag.find()`

![[Mongod4.png]]

----------
# Notas
Una base de datos es sql, cuando tiene el modelo tradicional de bases de datos que son tablas,columnas,filas,etc. Y cuando no tiene esto es una base de datos NOSQL

-------
# Creditos
Writeup Oficial HackTheBox