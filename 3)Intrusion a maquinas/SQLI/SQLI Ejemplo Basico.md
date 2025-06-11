SQL Injection es una vulnerabilidad que permite a un atacante insertar código SQL malicioso en una consulta a la base de datos.  
Esto puede permitir robar, modificar o eliminar datos, e incluso tomar control del sistema.

----
# Enumeracion y explotacion

Tenemos un panel de logeo
![[Appointment7.png]]
Vamos a probar poner esto
```
' or 1=1 -- -
En la contraseña ponemos cualquiera, no hace falta ninguna especifica
```
Y ya con eso pudimos pasar el panel de logeo, es una inyeccion basica