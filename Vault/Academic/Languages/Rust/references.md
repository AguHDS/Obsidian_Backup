---
tags:
  - basic
  - POO
  - rust
---
---

#### Que es una referencia?

Es una forma de acceder a un valor en memoria sin copiarlo. Es como un alias o puntero simplificado.

**En Rust**, la idea es la misma (un alias que apunta a un valor), pero con reglas mucho más estrictas:

-  Una referencia nunca puede ser nula.
-  No puedes tener referencias inválidas (dangling).
-  El compilador controla **ownership** y **borrow checking** para garantizar seguridad en memoria.

---

#### Referencia mutable


Una **referencia mutable** permite **modificar** el valor al que apunta, a diferencia de una referencia normal que solo permite leerlo. Tambien modifica el valor ORIGINAL.

Reglas clave en Rust:

-  Solo puede existir **una referencia mutable** a un valor en un momento dado.
-  No puede coexistir con referencias inmutables al mismo valor mientras la mutable esté activa.
-  El compilador garantiza que no habrá condiciones de carrera ni datos inconsistentes.

---

#### Desreference

La **desreferencia** es el acto de acceder al **valor real** al que apunta una referencia. En Rust, una referencia (`&T` o `&mut T`) **no es el valor en sí**, sino un **puntero seguro** hacia él. Para usar o modificar el valor, necesitas decirle explícitamente al compilador: “Quiero el valor que está aquí, no la referencia en sí”. Ese “decírselo” se hace con `*`

```rust
let mut x = 10;
let y = &mut x; // "y" es una referencia mutable a x

*y += 5; // desreferenciamos y modificamos el valor original de x
println!("{}", x); //imprime 15
```

-  `y` es solo un alias a `x`.
-  `*y` es **el valor real de x**, que podemos modificar.

Clave:
-  Si solo quieres **leer** el valor, normalmente **no necesitas `*`** porque Rust hace **deref coercion** automáticamente en muchos casos:
```rust
let x = 10;
let y = &x;
println!("{}", y);  // Rust automáticamente desreferencia y imprime 10
```

-  Pero si quieres usarlo como valor directamente o pasarlo a operaciones que requieren el tipo original, a veces necesitas `*`
```rust
let x = 10;
let y = &x;
println!("{}", y);  // Rust automáticamente desreferencia y imprime 10
```

💡 **Regla rápida:**

-   Lectura: a veces Rust lo hace automáticamente.
-   Escritura/Modificación: siempre necesitas `*` para desreferenciar.