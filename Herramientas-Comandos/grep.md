Se usa para **buscar texto dentro de archivos** o resultados de otros comandos.  Permite encontrar líneas que contienen una palabra, frase o patrón específico.

```
grep -riE "Palabra|.*Palabra|Palabra.*"
Ejemplo: grep -riE "connect|.*connect|connect.*"
```

*Parametros*
-r: Búsqueda recursiva 
-i: Búsqueda sin distinción entre mayúsculas y minúsculas. 
-E: Uso de múltiples patrones. Buscamos líneas que empiezan con «Ejemplo», terminan con «Ejemplo» o tienen «Ejemplo» entre ellas.