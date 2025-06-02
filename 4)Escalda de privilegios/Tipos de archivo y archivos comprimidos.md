##### Enumeracion y explotacion junto(No se diferenciar): Mediante comprimidos ocultos
Encontramos esto en el home de floris y vamos a usar la herramienta [[xxd]] ya que esta en hexadecimal, para descifrar el contenido
![[Curling15.png]]
```shell
cat password_backup | xxd -r  > /tmp/pass
```
No da nada legible, asi que al aplicar un `file pass` veo que el tipo de archivo es bzip2, asi que por lo tanto es un comprimido, asi que empiezo a descomprimir                 |                 [[file]]
![[Pasted image 20250531234516.png]]
[[Comprimidores de archivos]]
```shell
bzip2 -d pass
file pass.out   //Veo que es un gzip
mv pass.out pass.gz
gzip -d pass.gz
file pass // Veo que es un bzip2 de nuevo
bzip2 -d pass
file pass.out // Es un archivo tar ahora
tar xf pass.out
cat password.txt
```

![[Curling16.png]]
*Contraseña:* 5d<wdCbdZu)|hChXll

Ya tenemos una contraseña que aparenta ser de floris, asi que vamos a intentar conectarnos por [[ssh]]

![[Curling17.png]]