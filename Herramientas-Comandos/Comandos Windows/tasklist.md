Son los procesos que estan corriendo en el sistema, es parecido a [[ps faux y ps -eo]]


```shell
tasklist /v | findstr PRTG 
```

![[Netmon17.png]]
En este ejemplo, estamos usando la herrramienta [[findstr]] para filtrar por la palabra PRTG

*Parametros*
 `/v` Es para ver el usuario que esta corriendo el proceso y un poco mas de informacion
