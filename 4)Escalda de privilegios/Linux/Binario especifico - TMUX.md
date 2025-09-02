#tmux

-------

Con [[ps faux y ps -eo]] usamos el comando `ps faux` y vemos el binario de *tmux*
   ![[Valentine15.png]]

[Articulo que explica la vulnerabilidad](https://int0x33.medium.com/day-69-hijacking-tmux-sessions-2-priv-esc-f05893c4ded0)
*Breve resumen:*
	Tmux creó un **socket UNIX** usando `-S /.devs/dev_sess` para compartir la sesión con otro usuario; si el socket de root tiene permisos `rw` para tu grupo, cualquiera puede **tomar control de la sesión y ejecutar comandos como root**.


Ahora vamos a usar el comando `tmux -S /.devs/dev_sess` para hacernos root, y listo ya podemos catear las flags

![[Valentine20.png]]
