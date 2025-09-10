---
tags:
  - rust
  - basic
---
---

#### Que es

Es una funcion anónima que
-  Puede declararse en cualquier parte del código (no solo al nivel de módulo como una `fn`)
-  Puede **capturar variables del entorno** (algo que una función normal no puede)
-  Puede ser asignada a una variable, pasada como parámetro o devuelta como resultado

Funcion normal y closure:
```rust
// Función normal
fn suma(a: i32, b: i32) -> i32 {
    a + b
}

// Closure
let suma = |a: i32, b: i32| a + b;
println!("{}", suma(2, 3)); // 5
```
`la sintáxis | ... | no es para **contener un valor**, sino que es la forma de declarar los parámetros de un closure. Es la mejor manera de identificarlas`

En resumen un closure es como una **función ligera, sin nombre, que además puede usar variables externas sin que se las pases como parámetros**.

---

#### Move

