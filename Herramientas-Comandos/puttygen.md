#puttygen

--------
Sirve para **crear y convertir claves SSH** para usar con PuTTY. Podés generar nuevas claves o convertir entre formatos PuTTY (.ppk) y OpenSSH.

-------
# Como instalar

```shell
sudo apt install putty
sudo apt install putty-tools
```

------
# Como usar
```shell
puttygen ArchivoQueTieneElTexto -O TipoDeArchivo -o NombreDelArchivoFinal
Ejemplo puttygen ssh-rsa -O private-openssh -o id_rsa
```
*Parametros:*
	`-O` Especifica la extension del archivo
	`-o` Especifica el nombre del archivo final