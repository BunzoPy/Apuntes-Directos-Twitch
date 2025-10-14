#robocopy

-----

Es una herramienta robuesta de ocpia de archbivos en windows es mas fiable que copy y xcopy

---
# Comando

```
robocopy \B Ubicacion DirectorioDestino NombreDelArchivo
Ejemplo: robocopy /B z:\Windows\NTDS . ntds.dit
```

*Parametros:*
	`/B` Es de backupmode. Indica que ciopie el archivo usando los privilegios SeBackupPrivilege/SeRestorePrivilege