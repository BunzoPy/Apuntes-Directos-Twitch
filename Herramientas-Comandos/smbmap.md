#smbmap

---

Es una herramienta de **enumeración y auditoría de compartidos SMB** (Server Message Block) muy usada en pentesting y pruebas de seguridad de redes Windows.

---
# Comando

```
smbmap -u Usuario -p Contraseña -P Puerto -H Hostname
Ejemplo smbmap -u "guest" -p "" -P 445 -H retro.vl
```


![[Retro15.png]]

*Parametros:*
	`-u` Usuario
	`-p` Contraseña
	`-P` Puerto
	`-H` Es para poner el hostname, sea el dominio o una ip