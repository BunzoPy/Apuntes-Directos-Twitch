- Nos permite hacer CTRL + C, para poder copiar y pegar
- CTRL + L para limpiar la pantalla. 
- Redimensiona bien las ventanas de por ejemplo las aplicaciones de nano o vim

```shell
script /dev/null -c bash
CTRL + Z
stty raw echo;fg
reset xterm
export SHELL=bash
export TERM=xterm
stty rows 40 columns 184
```
#### Explicacion de cada comando
- `script /dev/null -c bash` → Crea un pseudo-TTY lanzando Bash.
    
- `CTRL+Z` → Suspende la shell remota para poder reconfigurar tu terminal local.
    
- `stty raw echo; fg` → Pone el terminal en modo raw (sin buffering) con eco activado y reanuda la shell.
    
- `reset xterm` → Limpia y reinicia la terminal para el tipo xterm.
    
- `export SHELL=bash` → Define Bash como tu shell por defecto.
    
- `export TERM=xterm` → Indica a las aplicaciones que el terminal es xterm (colores y control de cursor).
    
- `stty rows 40 columns 184` → Ajusta filas y columnas del TTY al tamaño de tu ventana.
		-  Para saber las dimensiones de tu maquina, en tu consola usa stty size
		![[stty.png]]