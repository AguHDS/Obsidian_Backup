---
tags:
  - basic
  - javascript
---
---

#### Call Stack
El call stack es la pila de ejecución de tareas de js. Cada vez que se ejecuta una tarea (función) se guarda en esta pila. Las funciones asincrónicas que usen apis del navegador (como fetch, setTimeout, geolocalización, etc) son derivadas al navegador y las promesas, async await y callbacks son derivadas a una pila de ejecución especial que tiene prioridad para resolverse antes que las anteriores apis.

---
#### Event loop
El event loop es el proceso que verifica que este mecanismo funcione. Está permanentemente actualizando el call stack para desapilar las fn finalizadas y pasa las asincrónicas finalizadas al call stack para que sean eliminadas de la pila. Es como un observador que controla el proceso de ejecución de tareas.