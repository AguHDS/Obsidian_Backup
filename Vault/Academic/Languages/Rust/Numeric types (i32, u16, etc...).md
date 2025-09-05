---
tags:
  - basic
  - rust
---
---

#### Que es

Rust ofrece varios tipos primitivos para representar números. Se dividen en **enteros**, **flotante** y algunos tipos especiales para conteo o tamaños.

-  `i` = _signed_ -> positivos y negativos
-  `u` = _unsigned_ -> solo positivos
- Tamaño en bits: `8, 16, 32, 64, 128`

 Rangos comunes:

-  `i8`: -128 ..= 127
-  `u8`: 0 ..= 255
-  `i32`: -2,147,483,648 ..= 2,147,483,647
-  `u32`: 0 ..= 4,294,967,295

#### Tamaños dependientes de la máquina

-  `isize` / `usize`: enteros cuyo tamaño depende de la arquitectura (32 o 62 bits).

-  32 bits → rango de `i32` / `u32`
-  64 bits → rango de `i64` / `u64`

Se usan normalmente para **índices de arrays, [[vectors (rust)]] y tamaños**
```rust
let index: usize = 10;
let vec = vec![1, 2, 3];
println!("{}", vec[index]);
```

####  floats

Para decimales, Rust tiene dos tipos:

-  `f32`: 32 bits, precisión simple
-  `f64`: 64 bits, doble precisión (default)
```rust
let pi: f64 = 3.14159; // por defecto f64
let peso: f32 = 72.5;  // menos precisión, menos memoria
```
👉 Usá `f64` salvo que tengas una razón muy fuerte para optimizar memoria con `f32`


#### ✅ Cuando usar cada uno

-  Usá `i32` para la mayoría de cálculos → es el **default** y balancea rendimiento/memoria
-  Usá `usize` para **índices y tamaños** (arrays, vectores, slices)
-  Usá `u8` para **bytes** (ej: `Vec<u8>` para buffers)
-  Usá `i64`/`u64` para **números muy grandes** (ej: timestamps UNIX)
-  Usá `f64` por defecto para decimales
-  Solo bajá a `i16`, `u16`, `f32`, etc. cuando optimices memoria o trabajes con hardware/protocolos que lo requieran

