#lxd

-------

Es un sistema de **contenedores de sistema completo** (system containers), similar a Docker, pero con un enfoque distinto. Sirve para gestionar **contenedores Linux que se comportan como máquinas virtuales ligeras**, usando **LXC (Linux Containers)** como backend.

### ✅ ¿Qué lo hace especial?

- Cada contenedor tiene su **propio sistema operativo completo** (como Ubuntu, Alpine, etc.).
    
- Podés acceder a ellos con `lxc exec`, como si fuera SSH.
    
- Soporta imágenes de muchas distros (`ubuntu`, `debian`, `alpine`, etc.).
    
- A diferencia de Docker, LXD está más orientado a correr **sistemas completos**, no solo procesos individuales.

----

## Importate

Se usa con el comando lxc


# Se puede utilizar para escalar privilegios [[Lxd contenedor con montura]]