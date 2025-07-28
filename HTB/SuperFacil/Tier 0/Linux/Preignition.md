#easy #linux #ping #nmap #gobuster

---
# Guided Mode

1)El forzamiento de directorios es una técnica utilizada para comprobar muchas rutas en un servidor web con el fin de encontrar páginas ocultas. ¿Cuál es otro nombre para esto? (i) Inclusión de Archivos Locales, (ii) dir busting, (iii) hash cracking.
	dir busting

2)Qué switch usamos en el scan de nmap para especificar que queremos realizar detección de versiones
	-sV

3)¿Qué informa Nmap es el servicio identificado como ejecutándose en el puerto 80/tcp?
	http

4)¿Qué nombre de servidor y versión de servicio se ejecuta en el puerto 80/tcp?
	nginx 1.14.2

5)¿Qué interruptor usamos para especificar a Gobuster que queremos realizar dir busting específicamente?
	dir

6)Cuando usamos gobuster para dir busto, ¿qué switch añadimos para asegurarnos de que encuentra páginas PHP?
	-x php

7)¿Qué página se encuentra durante nuestras actividades de búsqueda?
	admin.php
	
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
*TTL:* Maquina linux
*Puertos:*
	`80` HTTP

-------
# Enumeracion con [[Gobuster]]

Entramos a la pagina principal y no vemos nada relevante

![[Preignation5.png]]

```
gobuster dir -u http://10.129.188.142 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64 -x .php,.txt,.bak.,html
```

![[Preignation6.png]]

Nos da un archivo admin.php en la url:  http://10.129.166.210/admin.php

Es un panel de login, que probando contraseñas y usuarios normales. Podemos entrar con las credenciales admin:admin

![[Preignation4.png]]

![[Preignition7.png]]

Y ya conseguimos la flag
