
Vemos con [[id]] que estamos en el grupo bugtracker
Usamos el  comando [[find]] ``find / -group bugtraker 2>/dev/null`` para buscar archivos que pueda ejecutar por estar en este grupo

![[Oopsie23.png]]
Y tenemos que podemos usar el binario */usr/bin/bugtracker*
Al listar los permisos que tiene con [[ls]] `ls -l` vemos que es [[SUID]] y el usuario es root. 
![[Oopsie25.png]]

Al ejecutarlo me pide un id de bug, ponemos prueba y vamos que lo que esta haciendo el binario es un cat

![[Oopsie24.png]]
Entonces sacamos la conclusion de que estamos ejecutando el [[cat]] como si fueramos root