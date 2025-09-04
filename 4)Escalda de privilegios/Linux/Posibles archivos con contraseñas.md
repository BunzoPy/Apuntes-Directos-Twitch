
Para esto vamos a usar el comando [[grep]]

Dentro de la carpeta /var/www/html/cdn-cgi usamos el comando grep -riE "connect|.*connect|connect.*"   para buscar posibles contraseñas 

![[Oopsie20.png]]

Da como resultado ``M3g4C0rpUs3r!``

#### Aclaracion
Podemos cambiar la palabra connect por la que querramos, y tenemos que buscar en carpetas especificas, ya que si buscamos desde la raiz vamos a tener muchos falsos positivos

grep -riE ".*password|password|password.*"