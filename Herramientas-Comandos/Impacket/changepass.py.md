#changepasspy

----

Sirve para **cambiar la contraseña de una cuenta de usuario en un entorno Windows/Active Directory** usando los protocolos de red que el dominio expone (principalmente **SAMR** o **Kerberos**).

-----
# Enumeracion y explotacion

```
netexec smb 10.129.234.44 -u 'BANKING$' -p 'banking'
```

![[Retro18.png]]
Nos da el error STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT
Buscando en internet este error encontramos el siguiente [articulo](https://medium.com/@offsecdeer/finding-weak-ad-computer-passwords-e3dc1ed220df)
Donde nos dice que es un windows 2000 aprox y que la contraseña es correcta, pero no nos deja entrar por cuestiones de seguridad, pero que si cambiamos la contraseña vamos a entrar sin problemas

Buscamos este script de impacket [[changepass.py]] con [[find]] usando el comando `find / -name changepasswd.py 2>/dev/null`

![[Retro19.png]]
Lo encontramos en esta ruta asi que lo vamos a ejecutar `python3 /usr/share/doc/python3-impacket/examples/changepasswd.py retro.vl/BANKING\$@10.129.234.44 -newpass 'exploit' -p rpc-samr`
Tenemos que poner el dominio/usuario/ip. Luego con `-newpass` ponemos la nueva contraseña, y el `-p` es para el pipe

![[Retro20.png]]
Luego comprobamos con [[netexec]] que la contraseña se cambio correctamente con el comando `netexec smb 10.129.234.44 -u 'BANKING$' -p 'exploit'`