#netexec

---
Es la version vieja de 1.3.0. En la actual ya no pasa

Se usa para reconocimiento de red, movimiento lateral y evaluaciones de seguridad, así como para ayudar con pruebas de autenticación automatizadas contra sistemas remotos en un entorno de Active Directory.
Lo podemos usar para hacer por ejemplo fuerza bruta, si tenemos una contraseña y un listado de posibles usuarios. Pero no se le puede pasar el rockyou ni diccionarios grandes, por que da error
	![[Cicada14.png]]
"too many values to unpack"

---
# Servicio [[smb]]


```
netexec smb 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```

*Parametros:*
	`Servicio` Puede ser smb,rpc/dcom,etc
	`Ip` Ip de la maquina
	`-u` Es para poner el usuario, puede ser un nombre o un archivo
	`-p` Es para la contraseña, puede ser la contraseña en texto o un diccionario
	`--users` Es para listar los usuarios del sistema
	`--shares` Para ver todas las carpetas y los permisos que tenemos
	`--no-bruteforce` No intenta adivinar contraseñas/usuarios adicionales, solo usa lo que le dimos
	`--module impacket-secretsdump` Es lo mismo que [[impacket-psexec]] todavia no lo use

![[Cicada16.png]]
Eetexec nos va a mostrar parámetros adicionales del usuario, como la descripción, membresias de grupo e información de perfil. A diferencia del [[impacket-lookupsid]] que solamente muestra los usuarios


-------
# Servicio LDAP

```shell
netexec ldap 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --bloodhound
```
*Parametros:*
	`Servicio` Puede ser smb,rpc/dcom,etc
	`Ip` Ip de la maquina
	`-u` Es para poner el usuario, puede ser un nombre o un archivo
	`-p` Es para la contraseña, puede ser la contraseña en texto o un diccionario
	`--bloodhound` Hace lo mismo que el [[bloodhound-python]]. Crea los archivos de enumeracion para el [[bloodhound]]
		``netexec ldap 10.129.234.71 --dns-server "10.129.234.71" -u Caroline.Robinson -p 'pawnedexploit123.' --bloodhound --collection all``
		lo tuve que hacer con bllodhoundpython por que bloodhound no me agarraba los archivos de netexec
		![[Baby14.png]]
	`--users` Enumera todos los usuarios
		Esto sirve para cuando no tengo ningun usuario por ejemplo`netexec ldap baby.vl -u''-p'' --users`
	`--query` Envia una consulta con el comando en especifico que estoy necesitando
		Ejemplo para enumerar todos los objetos``netexec ldap baby.vl -u''-p'' --query "(objectClass=*)" ""``
		Esta es otra query`"(sAMAccountName=*)" ""`
		Otra `"(objectClass=*)" ""`