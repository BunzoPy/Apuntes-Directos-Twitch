#easy #linux #nmap #ping #mysql

1)Durante nuestro escaneo, ¿qué puerto encontramos sirviendo a MySQL?
	3306

2)¿Qué versión de MySQL desarrollada por la comunidad está ejecutando el objetivo?
	MariaDB

3)Al utilizar el cliente de línea de comandos de MySQL, ¿qué interruptor debemos utilizar para especificar un nombre de usuario de inicio de sesión?
	-u

4)¿Qué nombre de usuario nos permite acceder a esta instancia de MariaDB sin proporcionar una contraseña?
	root
	
5)En SQL, ¿qué símbolo podemos utilizar para especificar dentro de la consulta que queremos mostrar todo lo que hay dentro de una tabla?
	*

6)En SQL, ¿con qué símbolo debemos terminar cada consulta?
	;

7)Hay tres bases de datos en esta instancia de MySQL que son comunes a todas las instancias de MySQL. ¿Cuál es el nombre de la cuarta que es exclusiva de este host?
	htb
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.95.232
nmap -p- --open --min-rate 5000 -n -Pn -vvv -sS 10.129.95.232 -oG allports
nmap -sC -p3306 10.129.95.232 -oN target
```

![[Sequel1.png]]

![[Sequel2.png]]

![[Sequel3.png]]
*TTL:* Maquina Linux
*Puertos:*
	`3306` [[mysql]]
*Datos Importantes:*
No se por que pero en el segundo escaneo de nmap el parametro -V no estaba andando, asi que solamente use el `-sC` para mandar los scripts de reconocimiento

------
# Intrusion por [[mysql]]

Miramos todas las bases de datos con `show databases;` despues entramos a la base de datos *htb* con `use htb;` ahora miramos las tablas con `show tables;` y luego dumpeamos todos los datos de la tabla *config* con el comando `select * from config;` Y listo ya visualizamos las columnas donde un valor es *flag* y su *value* es la flag de la maquina
![[Sequel5.png]]