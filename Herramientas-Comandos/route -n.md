`route -n`

Nos sirve para encontrar la ip de la maquina host, estando dentro del contenedor, 

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.19.0.1      0.0.0.0         UG    0      0        0 eth0
172.19.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0



| `Gateway` | `172.19.0.1` → La **puerta de enlace** o **router** al que se enviará ese tráfico. |     |
| --------- | ---------------------------------------------------------------------------------- | --- |


| `Genmask` | `0.0.0.0` → Máscara para una red "catch-all" (todas las IPs).        dependiendo el contexto, 0.0.0.0 vale todas las ips, pero en general si |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------- |


| `Flags` | `UG` → `U` indica que la interfaz está **activa (up)**, y `G` que usa una **gateway**. |
| ------- | -------------------------------------------------------------------------------------- |

| `Metric` | `0` → Prioridad de la ruta (más bajo = más prioridad). |
| -------- | ------------------------------------------------------ |


| `Ref` | `0` → No se usa casi nunca (referencias). |
| ----- | ----------------------------------------- |


| `Use` | `0` → Veces que se usó esta ruta (desde que se levantó la interfaz). |
| ----- | -------------------------------------------------------------------- |

| `Iface` | `eth0` → Se envía por la **interfaz de red `eth0`**. |
| ------- | ---------------------------------------------------- |
