#impacket-mssqlclient #microsoftsqlserver

---------
Sirve para conectarse a un servidor [[microsoft SQL server]] y ejecutar consultas SQL o, si tenés privilegios, incluso **comandos del sistema operativo (RCE)**.

-----
# Como conectarse

```shell
impacket-mssqlclient NombreDelDominio/Usuario@Ip -windows-auth
Ejemplo: impacket-mssqlclient ARCHETYPE/sql_svc@10.129.217.189 -windows-auth
```

![[Archetype10.png]]
*Parametros Adicionales:*
	`-windows-auth` Para usar autentificacion [[NTLM]] en lugar de autentificacion SQL

