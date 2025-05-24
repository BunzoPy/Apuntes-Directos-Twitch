```shell
ps -faux
```
*Parametros:*
`faux` El el conjunto de los siguientes parametros juntos
	`f` Muestra los procesos en forma de arbol jerarquicamente, para ver los procesos padres e hijos
	`a` all users, muestra los procesos de todos los usuarios
	`u` usa un formato que muestra el dueño del proceso, uso del cpu, memoria, etc
	`x` muestra tambien los procesos que corren en segundo plano




## ps -eo user,command
Este se suele usar para cuando creamos la utilidad del procmon.sh

```bash
ps -eo user,command
```

- `-e`: muestra todos los procesos
- `-o user,command`: columnas específicas

