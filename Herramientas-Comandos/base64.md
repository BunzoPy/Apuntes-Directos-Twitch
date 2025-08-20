#base64

------------

Se usa para **codificar** o **decodificar** datos en el formato **Base64**, que convierte datos binarios (como archivos o texto con caracteres especiales) en texto ASCII legible.

------------
# base64 para decodificar

```
echo -n "Texto en base64" | base64 -d; echo
Ejemplo: echo -n "SG9sYSBjb21vIGVzdGFzIGVzdG8gZXMgdW5hIHBydWViYQ==" | base64 -d; echo
```
*Parametros y comandos:*
`echo -n` Sirve para mostrar el texto sin agregar salto de linea al final
`base64 -d` Decodifica la cadena
`echo (El que aparece al final)` Da un salto de linea, para que no quede nada pegado del prompt anterior, o que no quede nada que dificulte leer el mensaje

---------
# base64 para codificar

```
echo "Texto en base64" | base64
Ejemplo: echo "Hola esto es una prueba" | base64
```

*Parametros:*
`base64` Codifica a base64 la cadena que pusimos anteriormente 