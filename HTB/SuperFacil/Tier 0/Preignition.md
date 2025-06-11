Aca trabajamos enumeracion con [[FFUF]] y gobuster

---

1)El forzamiento de directorios es una técnica utilizada para comprobar muchas rutas en un servidor web con el fin de encontrar páginas ocultas. ¿Cuál es otro nombre para esto? (i) Inclusión de Archivos Locales, (ii) dir busting, (iii) hash cracking.
	dir busting

2)Qué switch usamos en el scan de nmap para especificar que queremos realizar detección de versiones
	-sV

3)¿Qué informa Nmap es el servicio identificado como ejecutándose en el puerto 80/tcp?
	http

4)¿Qué nombre de servidor y versión de servicio se ejecuta en el puerto 80/tcp?
	nginx 1.14.2
	Esto lo sacamos del escaneo de nmap

5)¿Qué interruptor usamos para especificar a Gobuster que queremos realizar dir busting específicamente?
	dir

6)Cuando usamos gobuster para dir busto, ¿qué switch añadimos para asegurarnos de que encuentra páginas PHP?
	-x php

7)¿Qué página se encuentra durante nuestras actividades de búsqueda?
	admin.php
	Resultado de este escaneo `gobuster dir -u http://10.129.166.210 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -x php -t 20`
	Con `ffuf -u http://10.129.166.210/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php -t 20` da el mismo resultado
	
8) ¿Cuál es el código de estado HTTP reportado por Gobuster para la página descubierta?
	200

----------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.166.210
nmap -sS -p- --open -vvv --min-rate 5000 -n -Pn 10.129.166.210 -oG allports
nmap -sCV -p80 10.129.166.210 -oN target
```

![[Preignition1.png]]

![[Preignition2.png]]

![[Preignition3.png]]

Por el ttl sabemos que es una maquina linux, y que el puerto 80 esta abierto corriendo un servicio http

-------
# Enumeracion con [[FFUF]]

#### Con gobuster como indica la maquina
![[Preignition4.png]]

#### Fffuf como usamos nosotros
![[Preignition5.png]]

Nos da un directorio http://10.129.166.210/admin.php
Es un panel de login, que probando el usuario admin y contraseña admin   entramos y tenemos la flag

![[Preignition6.png]]