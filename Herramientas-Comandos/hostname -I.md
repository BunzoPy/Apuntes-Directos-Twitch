Te da **la IP que el sistema asigna a su propio nombre de host**, no necesariamente la IP de tu interfaz de red.
![[Goodgames16.png]]


# A tener en cuenta

**`hostname -i`**  
Muestra la dirección IP asociada al nombre del host, según lo definido en el archivo `/etc/hosts`.  
Por ejemplo, puede mostrar `127.0.1.1`, que es una IP local usada internamente por el sistema.  
No siempre representa tu IP real en la red.

---

**`ifconfig`**  
Muestra las IP reales de tus interfaces de red, como `eth0`, `wlan0`, etc.  
Por ejemplo, puede mostrar `192.168.1.45`, que es la IP que realmente usás para conectarte a la red local o a internet.  
Este comando es más útil para saber tu IP real.