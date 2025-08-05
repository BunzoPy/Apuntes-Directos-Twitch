---
title: Writeup broker - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - broker
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina broker en Hack The Box.
keywords:
  - writeup broker
  - hack the box broker
  - resolución máquina broker
  - broker hack the box
  - htb broker
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: 
- 📺 **Resolución en vivo (completa)**: 
- 🧠 **Explicación resumida**: 

------------

#easy #linux #nmap #ping 

-----------
# Guided Mode

1)¿Qué puerto TCP abierto está ejecutando el servicio ActiveMQ?
	61616

2)¿Cuál es la versión del servicio ActiveMQ que se está ejecutando en el equipo?
	5.15.15

3)¿Cuál es el CVE-ID de 2023 para una vulnerabilidad de ejecución remota de código en la versión de ActiveMQ que se ejecuta en Broker?
	CVE-2023-46604

4)¿Con qué usuario se ejecuta el servicio ActiveMQ en el broker?
	




--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.243
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.243 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80,1883,5672,8161,43111,61613,61614,61616 10.10.11.243 -oN target
```

![[Broker9.png]]

![[Broker8.png]]

![[Broker7.png]]

![[Broker6.png]]

![[Broker5.png]]

![[Broker4.png]]

![[Broker3.png]]
*TTL:* Maquina linux
*Puertos Importantes*
	`22` [[ssh]]
	`80`HTTP
	`61616` ActiveMQ

-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.243
```

![[Broker2.png]]

![[Broker1.png]]

La informacion relevante que encuentro es que esta corriendo un Nginx

--------
# [[CVE-2023-46604]]

Entramos a la url del *ActiveMQ* http://10.10.11.243:61616/ nos salen estos caracteres no legibles, pero podemos entender que esta corriendo la version 5.15.15
![[Broker13.png]]


![[Broker11.png]]

![[Broker12.png]]

![[Broker10.png]]


