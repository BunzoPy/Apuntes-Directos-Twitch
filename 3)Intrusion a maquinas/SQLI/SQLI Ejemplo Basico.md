#SQLI

---------

SQL Injection es una vulnerabilidad que permite a un atacante insertar código SQL malicioso en una consulta a la base de datos.  
Esto puede permitir robar, modificar o eliminar datos, e incluso tomar control del sistema.

----
# Enumeracion y explotacion para paneles de logeo

Tenemos un panel de logeo
![[Appointment7.png]]
Vamos a probar poner esto
```
' or 1=1 -- -
En la contraseña ponemos cualquiera, no hace falta ninguna especifica
```
Y ya con eso pudimos pasar el panel de logeo, es una inyeccion basica



# Explicacion de la peticion

Esta es la peticion por detras del panel de logeo
*SELECT * FROM usuarios WHERE usuario = 'test' AND contraseña = 'loquesea';*
Si la comentamos con `' or 1=1 -- -` la peticion se enviaria asi
*SELECT * FROM usuarios WHERE usuario = ''or 1 =1;* 
Y como 1=1 es verdadero, toda la peticion pasa a ser verdadera, y nos envia a la siguiente paguina del panel de logeo
Como el primer usuario normalmente es *admin*, nos vamos a conectar como *admin*
