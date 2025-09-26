#ntpdate

----

Sirve para cuando queremos entrar en kerberos, ponemos la ip de la maquina victima y nos coloca la misma hora en nuestra maquina

---
# Como instalarlo

```shell
apt install ntpdate
```

-----
# Comando

```shell
ntpdate IpMaquinaVictima
Ejemplo: ntpdate 10.129.112.167
```


---
# Solucion en virtualbox cuando no cambia la hora

Cuando me intentaba cambiar la hora para conectarme a kerberos no me dejaba, pero después de estar buscando encontré que virtualbox me cambiaba la hora, asi que tuve que deshabilitar la sincronizacion horaria de esta forma con este [articulo](https://es.askingbox.com/tutorial/virtualbox-cambiar-la-fecha-y-la-hora)

`VBoxManage setextradata "<NombreMV>" "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled" "1"`
![[Administrator47.png]]