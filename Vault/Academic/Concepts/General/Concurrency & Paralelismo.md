---
tags:
  - general
---
---

#### Concurrency

Una app concurrente es la que puede manejar multiples tareas a la vez sin bloquearse (por ejemplo varias llamadas a web APIs a la vez).
Como lo hace? se intercalan las ejecuciones dando de manera tan rápida que da la impresión de simultaneidad. Asi, aunque se tenga solo un núcleo/hilo, puede ir procesando de a poco una, y asi la otra.

**Concurrente = varias tareas en progreso, aunque no todas avancen exactamente al mismo tiempo**

---
#### Paralelismo

El paralelismo es cuando mas de una tarea se ejecutan al mismo tiempo usando diferentes nucleos del CPU.

Ejemplo: en una máquina de 4 núcleos, podés correr 4 hilos de verdad al mismo tiempo.

Suele usarse para **partir un trabajo pesado en varias partes** y resolverlo más rápido (ej: cálculos científicos, renderizado 3D, procesamiento de imágenes).

**Paralelismo = ejecución simultánea real en hardware distinto**

---

#### Multithread

Es una técnica, un programa multithread puede usarse para concurrencia o paralelismo, dependiendo de si cumple la definición de concurrencia o paralelismo


---

#### Como funciona en el S.O

El sistema operativo es el encargado de asignar el paralelismo y la concurrencia a los núcleos para las aplicaciones que se están ejecutando, esto lo hace con un planificador (scheduler).
Dependiendo de la prioridad y del estado de ejecución (por ejemplo, un juego en primer plano), el SO puede darles **más paralelismo**, asignando hilos a núcleos disponibles para que se ejecuten en paralelo real.  
El resto de las aplicaciones continúan activas de forma **concurrente**, turnándose entre los núcleos y dando la ilusión de que todas se ejecutan al mismo tiempo.