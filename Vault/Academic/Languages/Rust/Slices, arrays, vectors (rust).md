---
tags:
  - rust
  - basic
---
---

#### Arrays en rust
En rust, los tipos de arrays necesitan longitud

```rust
let a: [i32, 4] = [1,2,3,4]
let b: [i32; 4] = [4, 5, 6, 7];
```
`a` y `b` **tienen tipos diferentes** y Rust **no permite operaciones entre arrays de tamaños distintos** sin convertirlos a slices o vectores

---
#### Slices

Un slice es una referencia que apunta a un array o vector.
Es una **vista de un arreglo** (o de un vector), que no tiene tamaño fijo conocido en tiempo de compilación.
No es dueño de los datos → solo tiene **puntero + longitud**.

```rust

// ejemplo con array
let arr = [10, 20, 30];
let s: &[i32] = &arr;  // s es un slice

// ejemplo con vector
let v = vec![10, 20, 30, 40];
let slice: &[i32] = &v[1..3];    // apunta a [20, 30]
```
`no copia los datos**: solo apunta al arreglo/vector existente`

##### Cuanod usar Slices

Querés pasar datos sin copiar:
Una función no necesita saber si le das un array, un vector o un pedazo de ellos → solo le interesa ver una lista de elementos

```rust
fn imprimir(slice: &[i32]) {
    for n in slice {
        println!("{n}");
    }
}

let arr = [1, 2, 3];
let v = vec![4, 5, 6];

imprimir(&arr);       // funciona con arrays
imprimir(&v);         // funciona con vectores
imprimir(&v[1..]);    // funciona con una parte del vector
```

---

#### Vectors

Un **vector** en Rust es un **arreglo dinámico en el heap**
```rust
let mut v: Vec<i32> = Vec::new();
v.push(20);
v.push(40);
```

Un vector es:
-  **Dinámico:** puedes agregar o quitar elementos en tiempo de ejecución
-  **Mutable (si se declara `mut`):** puedes cambiar su tamaño y contenido
-  **Slice de vector:** puedes obtener un slice de un vector (`&v[0..2]`) para pasar solo una “vista” de los datos

##### Cuando usar vectores

-  Cuando **no conoces la longitud de los datos en tiempo de compilación**.
-  Cuando necesitas **agregar o eliminar elementos dinámicamente** (`push`, `pop`, `remove`).
-  Cuando quieres **pasar datos dinámicos entre funciones** pero sin preocuparte del tamaño.


---

#### Comparación

|Concepto|Tipo en Rust|Tamaño|Mutabilidad|Dónde se almacena|
|---|---|---|---|---|
|**Array** `[T; N]`|`[i32; 3]`|Fijo, compile-time|Mutable o no según declaración|Stack|
|**Slice** `&[T]`|`&[i32]`|Dinámico, runtime|Solo referencia a mut o no|Apunta a stack o heap|
|**Vector** `Vec<T>`|`Vec<i32>`|Dinámico|Mutable (si `mut`)|Heap|