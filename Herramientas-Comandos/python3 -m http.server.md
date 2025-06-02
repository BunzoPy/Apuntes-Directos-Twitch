Levanta un **servidor web HTTP en el puerto 80** usando Python, que sirve los archivos del directorio actual. Es útil para **compartir o descargar archivos rápidamente** desde otra máquina mediante el navegador o `curl/wget`.

```shell
python3 -m http.server Puerto
Ejemplo: python3 -m http.server 80
```

*Parametros:*
	`-m` Permite ejecutar modulos directgamente como si fueran programas
		`http.server`  Es un modulo includo en python que le vantar un servidor http sin necesidad ed escribir codigo