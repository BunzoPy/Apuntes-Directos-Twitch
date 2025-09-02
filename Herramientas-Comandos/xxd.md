#xxd

----

Convierte datos binarios a hexadecimal y para facilitar nuestra lectura también nos da una representación en ASCII

```shell
xxd -r -p ArchivoQueTieneLaCadena > ArchivoQueVaAAlmacenarElOutput; echo
Ejemplo: xxd -r -p key.txt > id_rsa; echo
```
*Parametros:*
`-r` Es de reverse, convierte de hexadecimal a binario
`-p` solo toma los numeros en hex del input que le estamos pasando
`echo` El echo del final es para hacer un salto de linea y que no se mezcle con el prompt anterior


  ![[Valentine8.png]]
  