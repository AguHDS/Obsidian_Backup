---
tags:
  - rust
  - basic
---
---

##### Arc
Es un puntero seguro que:

-  Permite que múltiples **hilos** compartan la misma estructura de datos de manera segura. 
-  Incrementa automáticamente un contador cuando se clona y decrementa cuando se suelta.
-  Cuando el contador llega a cero, la memoria se libera.

```rust
use std::sync::Arc;

let a = Arc::new(5);
let b = Arc::new(&a); // b y a apuntan al mismo valor
```

`Arc` permite **compartir el ownership de un valor entre varios hilos de manera segura**:
-  Sin `Arc`, solo podrías mover la propiedad del `HashMap` a **un solo hilo**.
-  `Arc` hace que todos los hilos tengan una **referencia contada atómica** al mismo `HashMap`.
-  Cuando el último `Arc` se suelta, Rust libera la memoria automáticamente.

---

##### Mutex

Es un **candado** que asegura que solo un hilo a la vez pueda acceder a un valor mutable.

-  Evita condiciones de carrera al modificar datos compartidos.
-  Para acceder al valor, debes **bloquear** (`lock`) el mutex, lo que te da un `MutexGuard`
```rust
use std::sync::Mutex;

let m = Mutex::new(5);

{
    let mut num = m.lock().unwrap(); // bloquea el mutex
    *num += 1;
} // el mutex se desbloquea automáticamente al salir del bloque
```

-  **Deadlock** ocurre cuando dos (o más) hilos **esperan mutuamente que el otro libere un recurso**, y ninguno puede continuar.
-  **No usar `Mutex` no provoca deadlock**, sino **condiciones de carrera y errores de memoria**.
-  **Deadlock** aparece solo **cuando usas `Mutex` (u otros locks) y los hilos se bloquean entre sí** en un ciclo de espera.