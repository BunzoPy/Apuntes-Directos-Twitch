#tmux

-------

[Articulo que explica la vulnerabilidad](https://int0x33.medium.com/day-69-hijacking-tmux-sessions-2-priv-esc-f05893c4ded0)

Con [[ps faux y ps -eo]] usamos el comando `ps faux` y vemos el binario de *tmux*
   ![[Valentine15.png]]

[Articulo que explica la vulnerabilidad](https://int0x33.medium.com/day-69-hijacking-tmux-sessions-2-priv-esc-f05893c4ded0)

Ahora vamos a usar el comando `tmux -S /.devs/dev_sess` para hacernos root, y listo ya podemos catear las flags

![[Valentine20.png]]


-------
# Notas

Crea o conecta una sesión de tmux con un **socket personalizado** en `/.devs/dev_sess`.  Esto permite mantener la sesión activa y compartida, independiente de la sesión de terminal normal.