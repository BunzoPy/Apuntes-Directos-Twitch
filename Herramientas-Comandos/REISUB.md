Es una secuencia de teclas que se utiliza en sistemas Linux para reiniciar de forma segura cuando el sistema se congela, responde o se traba la maquina. Al presionar Alt + Print Screen (o SysRq) y luego escribir REISUB, se envía una serie de señales al kernel para intentar un reinicio controlado, minimizando el riesgo de corrupción de datos.

Aquí está el desglose de cada letra en la secuencia:

- **R (Raw):** Restaura el teclado al modo normal de operación.
- **E (Terminate):** Envía una señal SIGTERM a todos los procesos, indicándoles que se cierren de manera ordenada.
- **I (Kill):** Envía una señal SIGKILL a los procesos que no respondieron a SIGTERM, forzando su cierre.
- **S (Sync):** Sincroniza los sistemas de archivos, escribiendo los datos pendientes en el disco.
- **U (Umount):** Desmonta todos los sistemas de archivos.
- **B (Reboot):** Reinicia el sistema