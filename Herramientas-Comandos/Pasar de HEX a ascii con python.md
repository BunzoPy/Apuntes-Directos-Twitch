

Abri el  interprete de python con `python` despues se importa la libreria binascci con el comando `import binascii` luego vamos a poner todos los numeros en hexadecimal dandoselos a una variable poniendo

```
s='50 40 73 73 77 30 72 64 40 31 32 33 21 21 31 32 33 1 3 9 17 18 19 22 23 25 26 27 30 31 33 34 35 37 38 39 42 43 49 50 51 54 57 58 61 65 74 75 79 82 83 86 90 91 94 95 98 103 106 111 114 115 11 9 122 123 126 130 131 134 135'
```

Y luego para terminar vamos a usar la libreria que importamos al principio con ``binascii.unhexlify(s.replace(' ',''))`` usando el replace para sacar los espacios

![[Antique11.png]]
![[Antique12.png]]
Y nos da la contraseña *P@ssw0rd@123!!123*


---------

# Si no tuviera los espacios en el codigo HEX

Importaria la libreria con `import binascii` se lo asignaria a una variable en este caso le ponemos d con `d='5040737377'` y ahora usamos la libreria que importamos al principio con `binascii.unhexlify(d)`

![[Antique13.png]]
Y si queremos sacarle los saltos de linea y aparte la *b* que aparece al principio vamos a usar *.strip()* para el salto de linea y *decode()* para sacar la *b* `binascii.unhexlify(d).strip().decode()`
![[Antique14.png]]