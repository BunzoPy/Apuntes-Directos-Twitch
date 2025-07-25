#wget

------

Es una herramienta de línea de comandos que permite **descargar archivos desde la web** usando HTTP, HTTPS o FTP.

```
wget http://IpOPagina/NombreDelArchivo
Ejemplo: wget http://10.10.16.16/nc64.exe
```

*Parametros opcionales*
``-outfile`` Esto es por si queremos cambiarle el nombre al archivo cuando se descargue, por ejemplo podemos usar `-outfile bunzo` para que el archivo que decargamos se llame bunzo

*Parametros opcionales windows*
`-UseBasicParsing`Le dice a PowerShell que **no use el motor de Internet Explorer** para interpretar la respuesta (lo cual no está disponible en muchos entornos) y que simplemente descargue el archivo de forma cruda.