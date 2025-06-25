MongoDB es una base de datos NoSQL orientada a documentos que guarda la información en formato similar a JSON (BSON).  
Es flexible, escalable y muy usada para aplicaciones web modernas y datos no estructurados.
Normalmente corre por el puerto 27017

# Como instalarlo
Esto nos va a dejar en la carpeta bin, un binario ejecutable que se llama .mongosh
```shell
curl -O https://downloads.mongodb.com/compass/mongosh-2.3.2-linux-x64.tgz
tar xvf mongosh-2.3.2-linux-x64.tgz
cd mongosh-2.3.2-linux-x64/bin
```

# Como conectarse a la base de datos
```shell
./mongosh mongodb://Ip:Puerto
Ejemplo: ./mongosh mongodb://10.129.228.30:27017
```
*Parametros*
`./mongosh` Es el nombre del ejecutable
`Ip` Ip de la victima
`Puerto` Puerto de la victima

# Como visualizar contenido de archivos

`show dbs` Es para visualizar todas las bases de datos
`use NombreBaseDeDatos` Seleccionamos la base de datos que queremos usar
`show collections` Es como una tabla en sql que tiene documentos
`db.NombreDelArchivo.find().pretty()` Esto sirve para visualizar el contenido del archivo que queremos ver

Ejemplo de una intrusion real
```mongodb
show dbs
use sensitive_information
show colletions
db.flag.find().pretty()
```

![[Mongod4.png]]