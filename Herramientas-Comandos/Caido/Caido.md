#caido

--------

Es una herramienta para pruebas de seguridad web, similar a [[burpsuite]], pero de código abierto y basada en navegador.  
Permite interceptar, modificar y analizar solicitudes HTTP/S entre el cliente y el servidor.  
Es útil para pentesters que buscan una alternativa liviana a BurpSuite con interfaz moderna.

------
# Automate

#### Para seleccionar el campo que queremos fuzzear, lo seleccionamos con el mouse y apretamos en el  simbolo *+* que esta arriba a la derecha
![[Caido5.png]]

#### Podemos seleccionar el archivo que queremos en la parte de *Selected file*
![[Caido6.png]]
Uso estos diccionarios de seclist
Para usernames: /usr/share/SecLists/Usernames/top-usernames-shortlist.txt 
Para contraseñas: /usr/share/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt

#### En la pestaña de *Preprocessors* podemos seleccionar si queremos que URL encodee los caracteres

![[Caido7.png]]

#### Pestaña de *Settings* podemos seleccionarla cantidad de workers que serian los hilos, en mi caso todavia estoy probando la cantidad perfecta, pero con 40 voy bien

![[Caido8.png]]
### Tipo de ataque

``Sequential``
	Envías los datos uno por uno en orden, ideal para análisis paso a paso.
	_Ejemplo:_ Probar una lista de usuarios con una contraseña fija para detectar cuál existe.
``All``
	Combina elementos de dos listas por posición (índice) y los prueba juntos.
	Probar usuario1 con pass1, usuario2 con pass2, para validar credenciales emparejadas.
``Parallel``
	Ejecutas muchas pruebas al mismo tiempo para ir más rápido.
	Lanzar múltiples intentos de login simultáneos para acelerar el ataque.
``Matrix`` 
	Genera todas las combinaciones posibles entre dos listas para probar todo.
	Probar cada usuario con todas las contraseñas para un ataque completo de fuerza bruta.
![[Caido9.png]]


## Filtrar las respuestas

![[Caido10.png]]
Lo podemos hacer por *status*, *length* o nos deja poner campos por lo que querramos filtrar, por ejemplo si ponemos *resp.code.ne:200* aca seria que queremos filtrar por codigo de respuesta que no sea igual a 200


------
# Como descargarlo

[Link del proyecto github](https://github.com/caido/caido/releases) yo en mi caso como tengo parrot me voy a descargar el que dice *Linux x86_64 (deb)*

![[Imagenes/Caido4.png]]

Y ahora desde la carpeta donde descargamos el archivo vamos a usar los siguientes comando en consola

```shell
sudo dpkg -i NombreDelArchivo
sudo apt -f install
```

--------
# Como abrirlo

Desde la consola vamos a usar el comando

```shell
burpsuite & disown
```
*Parametros*
	`&` Va a ejecutar la aplicacion en segundo plano
	`disown` Desvincula el proceso de la termina actual, evitanto que se cierre burpsuite si se cerrara la terminal actual de trabajo


![[Caido1.png]]

Apretamos el boton de *Start*
![[Caido2.png]]

Y listo ya esta abierto

![[Caido3.png]]

--------
# Como cargar diccionarios para fuerza bruta

En la pestaña de files, vamos a ir a donde dice arriba a la derecha Upload, y vamos seleccionar los que vamos a cargar
![[Caido4 1.png]]
En mi caso para cuando un panel de logeo en alguna pagina web, hago un pequeño ataque de fuerza bruta con los diccionarios */usr/share/SecLists/Usernames/top-usernames-shortlist.txt* para cuando son usuarios y */usr/share/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt* cuando son contraseñas
Copia estos archivos en alguna carpeta como descargas o el escritorio y despues las cargas en el caido


-----
# Atajos de teclado

*CTRL + R* Para mandar a la pestaña de *replay*
*CTRL + ENTER* Enviar la peticion cuando esta en el *replay*
*CTRL + M* Manda la consulta a la pestaña de *automate*

-------
