#trans

-----

Es un traductor como si fuera el google translate pero desde la consola

----
# Como instalarla

```shell
sudo apt install translate-shell
```

![[trans1.png]]

----
# Como usarla

```
trans -b :IdiomaAlQueQueremosTraducir "Texto"

Ejemplo: trans -b :es "Hello"
```

*Parametros:*
	`-b` Es para que solo nos imprima la traduccion sin encabazados ni metadatos
	`:IdiomaAlQueQueremosTraducir` Es el lenguaje al que queremos el texto en mi caso casi siempre va a ser español asi que seria *:es*
	`"Texto"` Es el texto que queremos traducir, recuerden ponerlo en doble comillas por las dudas
