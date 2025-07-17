Cuando un atacante envía parámetros con corchetes `[]` (o múltiples parámetros con el mismo nombre), PHP interpreta esos parámetros como **arrays** en lugar de strings, lo que puede **romper la lógica del código**, especialmente si el código espera strings simples.

Las funciones como `strcmp` que esperan strings pueden recibir arrays y comportarse de forma inesperada.

-----

Interceptamos con burpsuite la peticion

![[Base12.png]]
Y cambiamos la ultima linea por `username[]=test&password[]=pass` 
Asi ganamos acceso a la pagina upload.php
![[Base13.png]]
