1)¿Cuántos puertos TCP hay abiertos?
	2

2)¿Cuál es el dominio de la dirección de correo electrónico facilitada en la sección «Contacto» del sitio web?
	thetoppers.htb

3)En ausencia de un servidor DNS, ¿qué archivo de Linux podemos utilizar para resolver nombres de host en direcciones IP con el fin de poder acceder a los sitios web que apuntan a esos nombres de host?
	/etc/hosts

4)¿Qué subdominio se descubre durante la enumeración posterior?
	s3.thetoppers.htb

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.227.248
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.227.248 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.129.227.248 -oN target
```

![[Three1.png]]

![[Three2.png]]

Sabemos que es una maquina linux por el ttl cercano a 64, y que el puerto 22 esta abierto con un servicio ssh y el 80 con un apache

------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]
![[Three3.png]]
El script de nmap no conectaba y el whatweb nos da el mail con dominio thetoppers.htb que lo vamos a agregar a etc/hosts para probar

------

# Agregar dominio a /etc/hosts

```
10.129.227.248 thetoppers.htb
```

![[Three4.png]]

--------

# [[FFUF]]
Tuvimos que usar el gobuster, por que en el ffuf no salia nada y al ver el writeup, vemos que con gobuster si encontraba el subdominio, pero como sale con codigo de estado 404, no nos aparece en ffuf
```shell
gobuster vhost -u http://thetoppers.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```
![[Three5.png]]
Como resultado tenemos s3.thetoppers.htb



CAMBIAR LO DE FFUF DE SUBDOMINIOS Y PONER PARA ENUMERACION QUE VAMOS A USAR GOBUSTER

# Creditos
Writeup oficial HackTheBox