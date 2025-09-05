---
tags:
  - basic
  - rust
  - runtime
---
---

Rust tiene un modelo de memoria bastante distinto al de lenguajes como C/C++ porque no usa **GC (garbage collector)** ni exige que hagas `malloc`/`free` para liberar memoria manualmente. La clave está en su **sistema de ownership**, **borrow checker** y **lifetimes**.


#### Stack

-  Almacena datos de tamaño fijo y conocido en tiempo de compilación (ej: enteros, booleanos, arrays de tamaño fijo).
-  Operaciones rápidas tipo push/pop (último en entrar, primero en salir).
-  Ejemplo:
```rust
let x = 42 // Vive en el stack
```

#### Heap

-  Para datos cuyo tamaño puede cambiar en runtime o no es conocido en compilación.
-  Se reserva memoria dinámicamente.
-  Ejemplo clásico: `String` o `Vec<T>`.

```rust
let s = String::from("hola"); // metadata (puntero, len, capacity) está en el stack, 
                              // pero los caracteres "hola" viven en el heap
```


2. Cómo gestiona Rust la memoria en el heap?**

Rust no tiene un garbage collector, en su lugar:

-  Cada valor tiene un **dueño (owner)**.
-  Cuando el dueño sale de **scope**, Rust libera automáticamente la memoria (RAII, parecido a C++).

Cuando pasás variables que viven en el heap:
-  **Por defecto, se mueve el ownership** (no se copia la memoria).
-  Esto evita dobles liberaciones.

Esto se logra gracias al [[ownership]] y prestamos con [[references]]

Cuando ya no hay dueños válidos, la memoria heap se libera automáticamente en tiempo _determinístico_ (no como en GC, que es en algún momento indeterminado).

---

#### Diferencia con Event loop de Node

Antes de entender esto hay que saber que es el **runtime**.
- Un **runtime** es una librería (crate) que implementa:
    -  un **event loop** (espera de eventos I/O, timers, etc.)
    -  un **scheduler** (decide en qué hilo ejecutar cada tarea async)
    -  herramientas (canales, timers, sockets async, etc.)

Ejemplos de runtimes en Rust:
-  **Tokio** → el más usado, muy rápido y completo.
-  **async-std** → más parecido a Node o Go en estilo.

 Rust el lenguaje **solo te da la sintaxis** `async/await` y la abstracción `Future`. Para que eso se ejecute, necesitás un runtime que se encargue del “loop”.

##### Por qué es importante que Tokio (u otro runtime) sea **multithreaded**?

En Node.js:

-  Hay **un solo hilo de ejecución** para el JS.
-  Podés hacer I/O sin bloquear gracias al event loop, pero si hacés **tareas pesadas de CPU** (ej: procesar imágenes, encriptar, compilar, etc.) → bloqueás el loop y toda la app se frena
-  Node lo resuelve con _workers_ (worker_threads, cluster), pero son mecanismos externos al modelo principal

En Rust con Tokio (o cualquier runtime multithread):

-  El runtime puede **ejecutar futures en paralelo en varios hilos**.
-  O sea, si tenés muchas tareas pesadas o mucha I/O concurrente, el runtime reparte la carga entre cores del CPU
-  Esto significa que **no tenés un cuello de botella único** como en Node

Esto hace que con Rust, podemos hacer cosas que en Node no se podrían o le costaría, como por ejemplo:

**Comprimir un archivo grande, entrenar un modelo de ML, procesar audio/video **
-  **Node**: si lo hacés en el loop principal, congelás todo. Necesitás mandar esa tarea a un worker separado
-  **Rust**: podés tener futures que usen varios threads del runtime, o usar `std::thread` nativo. Se aprovechan todos los cores

Hay concurrencia y paralelismo real.