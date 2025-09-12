#abusobinariosmosh-server 

------

```shell
sudo -l
```

![[UnderPass8.png]]
Encontramos que podemos ejecutar el binario `/usr/bin/mosh-server` como cualquier usuario sin proporcionar contraseña. Y nos vamos a abusar de esto ejecutandolo como root 

Viendo este [articulo](https://www.hackingdream.net/2020/03/linux-privilege-escalation-techniques.html)

![[Underpass21.png]]
Si ejecutamos la instruccion `mosh --server="sudo /usr/bin/mosh-server" localhost` 

![[Underpass23.png]]

Ganamos acceso como el usuario root y ya podemos catear las flags

![[UnderPass7.png]]
