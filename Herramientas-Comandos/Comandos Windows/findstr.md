Es una herramienta de Windows para buscar texto dentro de archivos o salidas de otros comandos.  Funciona de forma similar a [[grep]] en Linux, permitiendo filtros por palabra, frases o patrones simples.  
Se usa comúnmente junto a pipes (`|`) para buscar resultados específicos en listas o logs


#### Ejemplo:
Aca estamos grepeando por la palabra PRTG

tasklist /v | findstr PRTG                 
	![[Netmon17.png]]
