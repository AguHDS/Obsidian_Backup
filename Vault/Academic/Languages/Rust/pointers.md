---
tags:
  - rust
  - basic
---
---

#### Pointers (Punteros)

En Rust **casi nunca trabajás con punteros crudos** (`*const T` o `*mut T`) salvo que programes bajo nivel o FFI (interoperar con C).  
En el día a día vas a usar **referencias** (`&T`, `&mut T`), que son punteros seguros administrados por el compilador.

```rust
fn main() {
    let mut x = 10;
    let r2 = &mut x;

    println!("Antes: x = {}", x);  // 10

    *r2 += 5; // desreferenciamos r2 para modificar el valor real de x

    println!("Después: x = {}", x); // 15
}
```
Aca usamos un puntero para desreferenciar la referencia y poder editar el valor real

---

#### Smart pointers

Son tipos de datos que encapsulan punteros y le agregan lógica extra (contadores, ownership, etc). Los más usados:

-  `Box<T>` → guarda un valor en el heap, y libera la memoria automáticamente al salir de scope.
-  `Rc<T>` → referencia contada (Reference Counted). Permite múltiples dueños de un mismo valor. 
-  `Arc<T>` → como `Rc<T>`, pero seguro para threads (Atomic Reference Counted).
-  `RefCell<T>` → permite mutabilidad en tiempo de ejecución (lo que normalmente se chequea en compile-time, acá se chequea en runtime).

Ejemplo con `Box`:
```rust
fn main() {
    let b = Box::new(42);   // guarda el 42 en el heap
    println!("b = {}", b);
}   // memoria liberada automáticamente aquí
```

---

#### Raw pointers (`*const T`, `*mut T`)

Estos son los punteros “crudos” como en C/C++.  
No tienen reglas de seguridad y **son peligrosos si no sabés lo que hacés** (pueden causar bugs, mal manejo en memoria, errores e incluso crashear el programa). 
Se usan solo en bloques `unsafe`.
```rust
fn main() {
    let mut x = 10;
    let r1: *const i32 = &x;    // puntero de solo lectura
    let r2: *mut i32 = &mut x;  // puntero mutable

    unsafe {
        println!("r1 = {}", *r1);
        *r2 += 5;
        println!("x = {}", *r2);
    }
}
```

---
#### Cómo se relaciona con ownership y lifetimes

✅ En resumen

-  Cada valor tiene un único dueño que libera la memoria.
-  Las referencias (`&`) no viven más que el valor al que apuntan.
-  El compilador verifica todo esto en tiempo de compilación.


-  Usás **referencias** (`&`, `&mut`) el 90% de las veces.
-  Usás **smart pointers** (`Box`, `Rc`, `Arc`, etc.) cuando necesitás comportamientos especiales.
-  Usás **raw pointers** (`*const`, `*mut`) solo en casos muy de bajo nivel o FFI.