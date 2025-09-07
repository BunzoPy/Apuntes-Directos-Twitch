Esto es una guia para saber que comando tengo que usar, para las vulnerabilidades mas comunes y enumerar

```shell
sudo -l
getcap -r / 2>/dev/null
find / -perm 4000 2>/dev/null
ps -faux            //Para ver si hay algun proceso raro corriendo
uname -r              //Se ve la version del kernel
uname -m              //Vemos si es de 32 o 64 bits el sistema operativo
lsb_release -a        //Para ver que distribucion de sistema operativo esta usando
env                   //Vemos todas las variables de entorno        
```

## En el caso de que haya archivos en el directorio del usuario

```
grep -riE "password|.*password|password.*              //Para ver si tiene algun archivo con contraseña
```

## Para ver tareas cron usar el [[PSPY]]
