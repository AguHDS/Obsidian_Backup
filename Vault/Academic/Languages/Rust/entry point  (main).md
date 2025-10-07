---
tags:
  - basic
  - rust
---
---

#### Que es main()

Es el punto de entrada de cualquier programa ejecutable.

Qué significa esto?

-  Cuando compilas y ejecutas un programa en Rust, el **runtime** de Rust (mínimo, no como en Java o JS) busca **qué función debe ejecutar primero**.
-  Solo puede haber un punto de entrada por binario ejecutable.
-  Esa función **siempre se llama `main`** y debe tener esta firma:

```rust
fn main() {
	// código que se ejecuta primero
}```

🆚 Diferencia con JavaScript

En JavaScript (Node, navegador, Deno):

-  El archivo se lee **de arriba hacia abajo** y se ejecuta lo que está en el "nivel superior" directamente.
-  No necesitas declarar un `main` porque el intérprete simplemente empieza a ejecutar el código en orden.

---
#####  Como funciona realmente?

Esto no significa que todo el codigo como funciones, metodos se tengan que volcar en main, eso seria un caos.

`main()` **solo sirve como punto de inicio**, para "orquestar" tu programa, todo lo demás se organiza en **módulos, archivos, funciones, structs, traits**, etc.
Por ejemplo, podría inicializar la configuración, iniciar el servidor, o arrancar un loop principal.
No “llama a todas las funciones de la app”

Ejemplo:
```rust
fn main() {
    iniciar_servidor(); // Orquesta todo desde aquí
}

fn iniciar_servidor() {
    cargar_config();
    escuchar_peticiones();
}

fn cargar_config() {
    // Lee archivos, env vars, etc.
}

fn escuchar_peticiones() {
    // Cada request llama a distintos handlers
    handle_get_user();
    handle_update_post();
}
```

Aquí, `main()` solo llamó a `iniciar_servidor()`, pero esa función a su vez llama a otras, y esas a otras
Así **se construye una “cadena de llamadas”**, no necesitas enumerar cada función en `main`


Resumen clave:

- `main()` es el **orquestador inicial**, no el “listado de todas las funciones de la app”.
-  Las funciones se llaman **cuando se necesitan**, ya sea desde otras funciones, eventos, threads, handlers, etc.
-  Esto es exactamente igual a lo que haces en JavaScript: tu archivo JS no llama todas las funciones, solo inicializa el server o un event loop, y luego las funciones se disparan según la lógica.