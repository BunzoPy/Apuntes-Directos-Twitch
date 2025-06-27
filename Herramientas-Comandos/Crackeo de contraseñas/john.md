Es una herramienta de **crackeo de contraseñas**. Se usa para **romper hashes** y recuperar las contraseñas en texto claro. Lo bueno es que no hay que especificar el tipo de hash, ya que lo detecta solo

```shell
john -w=Diccionario ArchivoConHash
Ejemplo: john -w=/usr/share/wordlists/rockyou.txt hash.txt
```

![[Responder6.png]]

---
# Ver contraseñas crackeadas previamente

#### Archivo que tiene todas las contraseñas crackeadas
Ubicacion del archivo: ``~/.john/.john.pot``

```shell
cat ~/.john/.john.pot
```
![[john1.png]]
Por ejemplo en este caso la contraseña se muestra al final y es badminton


#### Comando para ver contraseñas crackeadas de un archivo en especifico

```shell
john --show ArchivoConHash
Ejemplo: john --show hash
```
Mostraria la contraseña ya crackeada