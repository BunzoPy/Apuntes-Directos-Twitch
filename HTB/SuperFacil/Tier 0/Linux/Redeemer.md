---
title: Writeup redeemer - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - redeemer
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina redeemer  en Hack The Box.
keywords:
  - writeup redeemer
  - hack the box redeemer
  - resolución máquina redeemer
  - redeemer hack the box
  - htb redeemer
---
---------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+0/Linux/Redeemer)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=dlZ_WLULACI)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=dbm9Pz9e0eM)

---

#veryeasy #startingpoint #linux #redis #nmap #ping 

--------
# Guided Mode

1)¿Qué puerto TCP está abierto en la máquina? 
	6379

2)¿Qué servicio se está ejecutando en el puerto que está abierto en la máquina? 
	redis

3)¿Qué tipo de base de datos es Redis? Elija entre las siguientes opciones: (i) Base de datos en memoria, (ii) Base de datos tradicional
	in-memory database

4)¿Qué utilidad de línea de comandos se utiliza para interactuar con el servidor Redis? Introduzca el nombre del programa que introduciría en el terminal sin ningún argumento. 
	redis-cli

5)¿Qué bandera se utiliza con la utilidad de línea de comandos Redis para especificar el nombre de host? 
	-h

6)Una vez conectado a un servidor Redis, ¿qué comando se utiliza para obtener la información y las estadísticas sobre el servidor Redis? 
	info

7)¿Cuál es la versión del servidor Redis que se utiliza en el equipo de destino? 
	5.0.7

8)¿Qué comando se utiliza para seleccionar la base de datos deseada en Redis? 
	select

9)¿Cuántas claves hay en la base de datos con índice 0? 
	4
	
10)¿Qué comando se utiliza para obtener todas las claves de una base de datos? 
	keys *

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.48.19
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.129.48.19 -oG allports
extractPorts allports
nmap -sCV -p6379 10.129.48.19 -oN target
```
![[Redeemer1.png]]

![[Redeemer2.png]]
*TTL:* Maquina Linux
*Puertos:*
	`6379`[[redis]]

-----
# Intrusion con [[redis]]

Nos conectamos a la base de datos
```
redis-cli -h 10.129.48.19 -p 6379
```

Con el comando `info keyspace` vimos que base de datos estaban
	![[Redeemer3.png]]
Seleccionamos la base de datos 0 con el comando ``select 0``, despues visualizamos todas las keys con `keys *` y con `get flag` visualizamos el codigo de la flag

![[Redeemer4.png]]

