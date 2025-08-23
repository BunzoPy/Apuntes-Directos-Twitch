#XSS 

--------

Un **XSS (Cross-Site Scripting)** es una vulnerabilidad web que pasa cuando una aplicación no valida bien lo que un usuario escribe, y ese texto termina ejecutándose como **código JavaScript en el navegador** de otra persona.
Cuando veo un formulario probar XSS por que todos los navegadores interpretan java

------
### Tipos de XSS

- **Reflejado (Reflected):** el payload viaja en la petición y el servidor lo devuelve inmediatamente, sin guardarlo.
    
- **Almacenado (Stored):** el payload se guarda en el servidor (base de datos o comentarios) y se ejecuta cada vez que alguien carga la página.
    
- **Basado en DOM (DOM-based):** el payload no pasa por el servidor, se procesa solo en el JavaScript del navegador.

El **tipo de XSS** (reflejado, almacenado o DOM-based) **no depende del payload en sí**, sino de **cómo y dónde se procesa y se ejecuta** ese payload:

----
# Enumeracion

Para probar si es vulnerable vamos a usar los siguientes 2 payloads, y alguno puede llegar a funcionar, nos tendria que salir algun cartel en la pagina web

```shell
<img src=x onerror=alert(0)>

<script>alert("XSS")</script>
```