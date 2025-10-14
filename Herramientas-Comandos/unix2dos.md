#unix2dos

---

Cuando aplicás `unix2dos`, convertís cada `\n` en `\r\n`, y eso hace que el programa (que probablemente espera formato Windows) **reconozca correctamente los finales de línea** y lea todo el contenido.

-----
# Comando

```
unix2dos Archivo
Ejemplo: unix2dos exploit.txt
```
