El **cookie hijacking** es una técnica en la que un atacante roba las cookies de sesión de un usuario.  
Con esa cookie, puede **suplantar su identidad** y acceder a su cuenta sin necesidad de contraseña.  
Esto suele ocurrir en conexiones inseguras o por vulnerabilidades en la aplicación web.






------
# Ejemplo 1

En el link `http://10.129.207.226/cdn-cgi/login/admin.php?content=accounts&id=1`

![[Oopsie9.png]]
Vemos el id del usuario admin, ahora vamos a cambiar los valores de la cookie para robarnos las credenciales

Con el user id 34322 y el name de admin, que sacamos antes, vamos a apretar en el navegador CTRL + SHIFT + I
Se nos va a abrir esto y vamos a cambiar el valor role `guest` a `admin` y el valor de user `2233` a `34322`

![[Oopsie10.png]]
Reiniciamos y la pagina, y ya estamos como el usuario admin