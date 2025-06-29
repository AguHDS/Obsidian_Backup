---
tags:
  - advanced
  - Software_Arquitecture
---
links: [[Arquitectura de Sistemas]]

---

#### 🌐 ¿Qué es Serverless?

Serverless (sin servidor) no significa que no haya servidores. Los servidores siguen existiendo, pero tú como desarrollador no tienes que preocuparte por gestionarlos directamente. La infraestructura la maneja un proveedor (como AWS, Google Cloud, Vercel, etc.) y tú solo te enfocas en escribir funciones que hacen una tarea específica.


---

#### 🛠️ ¿Cómo funciona?

-  Subes **funciones** al proveedor (por ejemplo, una función que guarda datos o responde a una solicitud HTTP).
-  El proveedor **ejecuta esas funciones bajo demanda**: solo cuando alguien las llama.
-  No tienes que mantener un servidor encendido todo el tiempo.
-  Pagas solo por el tiempo que se ejecuta tu función (por ejemplo, 200ms), no por una máquina activa 24/7

--
#### 🧠 Ventajas

-  El tráfico se ajusta automaticamente a la cantidad de usuarios
-  Menor mantenimiento de infraestructura.
-  Muy buena opción para microservicios, APIs simples o tareas automatizadas.
-  Costos bajos si el uso no es constante.

---
#### ⚠️ Desventajas

-  **Cold starts:** si tu función lleva rato sin usarse, puede tardar unos segundos en activarse.
-  **Time limits:** suelen tener límites de duración (ej. 15 min máximo).
-  Más difícil depurar localmente (aunque hay herramientas para eso).
-  Puede haber **vendor lock-in** (quedas atado a un proveedor si no cuidas la portabilidad).

---
#### 🚀 ¿Cuándo usarlo?

-  APIs REST o GraphQL pequeñas o medianas.
-  Automatizaciones por eventos (cron, subida de archivos, etc.).
-  Prototipos o MVPs que necesitas lanzar rápido.
-  Funciones aisladas en apps más grandes.