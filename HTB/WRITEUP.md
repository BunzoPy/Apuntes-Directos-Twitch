# Reconocimiento

Primero, como siempre hacemos reconocimiento de los puertos con NMAP, y nos da este resultado

nmap -p- --open -sS -Pn -vvv -n --min-rate 5000 10.10.10.138 -oG allports
nmap -sCV -p80,22
![[Writeup1.png]]
Por lo tanto el puerto 22 de ssh y el 80 de apache estan abiertos.
Dandonos acceso a la pagina http://10.10.10.138/ y aparte, nos aparece detalle de que el robots.txt esta disponible asi que tambien tenemos el http://10.10.10.138/robots.txt

Entramos a la pagina http://10.10.10.138/ y vemos que nos dice que tiene proteccion ddos para que no se pueda hacer enumeracion, por mas de que intentamos distintos metodos con ffuf y gobuster no fue posible realizar enumeracion con fuzzing ni fuerza bruta
![[Writeup2.png]]

Cuando entramos a http://10.10.10.138/robots.txt vemos que nos da esta pista de que existe el directorio /writeup http://10.10.10.138/writeup/
![[Writeup3.png]]
Accediendo al codigo fuente de la misma podemos ver su gestor de contenido(CMS) que seria CMS Made Simple y podemos asumir que como es el copyrigth del 2004-2019 que puede estar usando la ultima version del 2019 que ya estaria muy obsoleta, en el mejor de los casos. De lo contrario estaria usando una version de años anteriores la cual estimo que seria mas vulnerable todavia
![[Writeup4.png]]


Spoileado que puede venir una sql injection y se m ocurre buscar en el searchsploit una vulnerabilidad al cms made simple, todavia no enumere nada de ssh podemos ver por esa parte 

