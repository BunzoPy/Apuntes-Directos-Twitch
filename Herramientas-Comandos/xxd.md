#xxd

----

Convierte datos binarios a hexadecimal y para facilitar nuestra lectura también nos da una representación en ASCII

```shell
xxd -r -p ArchivoQueTieneLaCadena > ArchivoQueVaAAlmacenarElOutput; echo
Ejemplo: xxd -r -p key.txt > id_rsa; echo
```
*Parametros:*
`-r` Es de reverse, convierte de hexadecimal a binario
`-p` es para que solo muestre los numeros hexadecimales sin cosas extras como direcciones o texto
`echo` El echo del final es para hacer un salto de linea y que no se mezcle con el prompt anterior

*Importante* Si vamos a usar un archivo de id_rsa para conectarnos por ssh, recordar darle permisos de ejecución o de lectura y escritura por que podemos llegar a tener problemas
```
chmod +x NombreDelArchivo
O si no llegara a funcionar
chmod 600 NombreDelArchivo
```

  ![[Valentine8.png]]
  ![[Valentine9.png]]