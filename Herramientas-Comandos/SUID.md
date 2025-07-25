#suid

------

El **SUID (Set User ID)** es un permiso especial que permite ejecutar un archivo con los privilegios del propietario, no del usuario que lo lanza.  
Se usa, por ejemplo, para que usuarios comunes puedan ejecutar programas como si fueran root.  
Si un binario con SUID está mal configurado, puede permitir escalada de privilegios.

------
# Como buscar achivos con permiso SUID con [[find]]

```
find / -perm -4000 2>/dev/null            /Este va a buscar archivos qeu tengan el SUID activado
find / -perm 4000 2>/dev/null             /Aca va a buscar archivos con el permiso 4000 exacto, que seria solamente el SUID y nada mas
```

-------
# Comando para ejecutar bash con privilegios SUID

```shell
bash -p
```

----------
# Como asignar permisos SUID con [[chmod]]

```shell
chmod u+s Archivo
Ejemplo: chmod u+s prueba.txt
```

![[SUID1.png]]