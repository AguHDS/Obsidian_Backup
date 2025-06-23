---
tags:
  - javascript
  - web_extension
---
### ¿Qué es un **content script**?

En una **extensión de navegador (como las de Chrome)**, un **content script** es un archivo JavaScript que se inyecta directamente en las páginas web que el usuario visita. Este script tiene acceso al **DOM** de la página, lo que le permite **leer y modificar el contenido**, detectar elementos visibles, responder a eventos del usuario, etc.

Por ejemplo:

-  Detectar cuándo aparece un nuevo post en LinkedIn.
-  Leer el texto de una publicación.
-  Insertar un comentario generado por OpenAI.

Los content scripts se definen en el `manifest.json` de la extensión y son **la manera estándar** de interactuar con el contenido de una página desde una extensión.