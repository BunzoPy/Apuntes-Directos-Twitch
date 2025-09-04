Esto es practicamente lo mismo que [[nc -nlvp 443]] pero para maquinas windows, ya que en estas no podemos hacer un [[Tratamiento de la TTY]].
Nos permite usar las flechas en la consola, borrar con CTRL + L, entre otras funciones que nos van a hacer las cosas mas simples en una maquina windows

```
rlwrap -cAr nc -lnvp Puerto
Ejemplo rlwrap -cAr nc -lnvp 443
```

*Parametros -cAr*
	`c` No guarda el historial y limpia la pantalla antes de cada ejecucion
	`A` Es para que ande mejor el autocompletado con tab
	`r` Es para que se repita el promp si se escribio antes