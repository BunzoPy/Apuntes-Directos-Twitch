AWS (Amazon Web Services) es la plataforma de servicios en la nube de Amazon, donde se pueden guardar archivos, correr servidores, bases de datos, etc.  
En ciberseguridad suele usarse para acceder a **S3 buckets**, que a veces están mal configurados y permiten ver o descargar archivos sin credenciales.
Normalmente esta cuando aparece un dominio s3.

-------
# Comandos para usar el aws


### Listar hosts
```shell
aws --endpoint-url PaginaWeb TipoDeServicio ls --no-sign-request
Ejemplo:aws --endpoint-url http://s3.thetoppers.htb s3 ls --no-sign-request
```
*Parametros:*
`--endpoint-url PaginaWeb` Sirve para poner a donde te vas a conectar
`TipoDeServicio` Estos pueden ser s3,ec2,rds o lambda
`ls` Sirve para listar los contenidos
`--no-sing-request` Especifica que el bucket es publico y no necesita credenciales
### Listar directorios

```shell
aws --endpoint-url PaginaWeb TipoDeServicio ls HostdelS3 --no-sign-request
Ejemplo: aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb --no-sign-request
```
*Parametros:*
`--endpoint-url PaginaWeb` Sirve para poner a donde te vas a conectar
`TipoDeServicio` Estos pueden ser s3,ec2,rds o lambda
`ls` Sirve para listar los contenidos
`HostDelS3` Tenemos que poner el host que listamos con el comando anterior
`--no-sing-request` Especifica que el bucket es publico y no necesita credenciales


![[Three7.png]]

-------

### Descargar archivos
Vamos a descargar el archivo index para ver si nos da alguna pista:
```shell
aws --endpoint-url PaginaWeb TipoDeServicio cp HostdelS3 . --no-sign-request
Ejemplo: aws --endpoint-url http://s3.thetoppers.htb s3 cp s3://thetoppers.htb/index.php . --no-sign-request
```

*Parametros:*
`--endpoint-url PaginaWeb` Sirve para poner a donde te vas a conectar
`TipoDeServicio` Estos pueden ser s3,ec2,rds o lambda
`cp` Copia el archivo
`HostDelS3` Tenemos que poner el host que listamos con el comando anterior
`.` Especifica que se va a descargar en el directorio actual de trabajo de nuestra maquina
`--no-sing-request` Especifica que el bucket es publico y no necesita credenciales


-----
### Subir archivos

```
aws --endpoint-url PaginaWeb TipoDeServicio cp NombreDelArchivo HostdelS3 --no-sign-request
Ejemplo: aws --endpoint-url=http://s3.thetoppers.htb s3 cp exploit.php s3://thetoppers.htb --no-sign-request
```

*Parametros:*
`--endpoint-url PaginaWeb` Sirve para poner a donde te vas a conectar
`TipoDeServicio` Estos pueden ser s3,ec2,rds o lambda
`cp` Copia el archivo
`NombreDelArchivo` Va el nombre del archivo que queremos subir
`HostDelS3` Tenemos que poner el host que listamos con el comando anterior
`--no-sing-request` Especifica que el bucket es publico y no necesita credenciales
