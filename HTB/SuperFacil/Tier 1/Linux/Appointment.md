En esta maquina vemos un poco de [[SQLI Ejemplo Basico]]

1)¿Qué significa la sigla SQL?
	Structured Query Language

2)¿Cuál es uno de los tipos más comunes de vulnerabilidades SQL?
	SQL Injection

3)¿Cuál es la clasificación 2021 OWASP Top 10 para esta vulnerabilidad?
	A03:2021-Injection

4) ¿Qué informa Nmap como el servicio y la versión que se están ejecutando en el puerto 80 del objetivo?
	Apache httpd 2.4.38 ((Debian))

5)¿Cuál es el puerto estándar utilizado para el protocolo HTTPS?
	443

6)¿Cómo se denomina una carpeta en la terminología de las aplicaciones web?
	directory

7)¿Cuál es el código de respuesta HTTP para los errores «No encontrado»?
	404

8)Gobuster es una herramienta utilizada para la fuerza bruta de directorios en un servidor web. Qué interruptor usamos con Gobuster para especificar que estamos buscando descubrir directorios, y no subdominios?
	dir

9)¿Qué carácter puede utilizarse para comentar el resto de una línea en MySQL?
	#

10)Si la entrada del usuario no se maneja con cuidado, podría interpretarse como un comentario. Utilice un comentario para iniciar sesión como admin sin conocer la contraseña. ¿Cuál es la primera palabra de la página web devuelta?
	Congratulations
	Esto lo dice una vez vulnerada la maquina
![[Appointment6.png]]

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.187.162   
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.129.187.162 -oG allports
nmap -sCV -p80 10.129.187.162 -oN target
```

![[Appointment1.png]]

![[Appointment2.png]]

![[Appointment3.png]]

Por el ttl sabemos que es una maquina linux y el puerto 80 esta abierto corriendo un apache

---
# [[Whatweb-wappalyzer]]

## No nos dio ningun dato relevante la enumeracion
```shell
whatweb htpp://10.129.187.162
nmap --script http-enum -p80 10.129.187.162 -oN webScan
```

![[Appointment4.png]]

![[Appointment5.png]]

-------
# Intrusion a la maquina con [[SQLI Ejemplo Basico]]

Vamos a la pagina http://10.129.187.162
![[Appointment7.png]]
Vemos este panel de logeo, asi que intentamos una inyeccion sql
```
' or 1=1 -- -
En la contraseña ponemos cualquiera, no hace falta ninguna especifica
```

![[Appointment6.png]]
Y listo ya estaria la flag, y resolvimos la maquina