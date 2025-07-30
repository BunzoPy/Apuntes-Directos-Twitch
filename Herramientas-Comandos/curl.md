El comando `curl` sirve para **hacer solicitudes HTTP (y otros protocolos) desde la línea de comandos**. Se usa para descargar contenido web, enviar datos, probar APIs, o interactuar con servidores remotos

*Parametros*
	`-k` Ignora los errores de certificados SSL/TLS
	`-K` Sirve para leer opciones desde un archivo de configuracion en lugar de pasarlos por una linea de comando en la shell
	`-s` Es de silent para que no muestre tanto verbose innecesario
	`-X` Indica por donde queremos hacer la peticion si por GET O POST
	`-v` Es de verbose para que muestre detallada la peticion y la respuesta
	`-O`  indica que guarde el archivo con el mismo nombre que tiene en el servidor | Ejemplo: ``curl -O https://downloads.mongodb.com/compass/mongosh-2.3.2-linux-x64.tgz``