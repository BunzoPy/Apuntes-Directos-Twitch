En esta maquina vemos [[mysql]]

1)Durante nuestro escaneo, ¿qué puerto encontramos sirviendo a MySQL?
	3306

2)¿Qué versión de MySQL desarrollada por la comunidad está ejecutando el objetivo?
	MariaDB

3)Al utilizar el cliente de línea de comandos de MySQL, ¿qué interruptor debemos utilizar para especificar un nombre de usuario de inicio de sesión?
	-u
	`-u` Usuario
	`-p` Contraseña
	

4)¿Qué nombre de usuario nos permite acceder a esta instancia de MariaDB sin proporcionar una contraseña?
	root
	
5)En SQL, ¿qué símbolo podemos utilizar para especificar dentro de la consulta que queremos mostrar todo lo que hay dentro de una tabla?
	*

6)En SQL, ¿con qué símbolo debemos terminar cada consulta?
	;

7)Hay tres bases de datos en esta instancia de MySQL que son comunes a todas las instancias de MySQL. ¿Cuál es el nombre de la cuarta que es exclusiva de este host?
	htb
![[Sequel4.png]]

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.95.232
nmap -p- --open --min-rate 5000 -n -Pn -vvv -sS 10.129.95.232 -oG allports
nmap -sC -p3306 10.129.95.232 -oN target
```

![[Sequel1.png]]

![[Sequel2.png]]

![[Sequel3.png]]

Por el ttl sabemos que es una maquina linux, esta el puerto 3306 corriendo con mysql,
No se por que pero en el segundo escaneo de nmap el parametro -V no estaba andando, asi que solamente use el `-sC` para mandar los scripts de reconocimiento

------

# Intrusion por [[mysql]]

```
mysql -h 10.129.95.232 -P 3306 -u root
show databases;
use htb;
show tables;
select * from config;
```

Usamos la base de datos htb y despues dumpeamos los datos de la tabla config
![[Sequel5.png]]