---
tags:
  - basic
  - rust
---
---

### Modulos
Los modulos (mods) en rust, te ayudan a organizar el codigo, como en otros lenguajes.


 #### Teoria
##### 1. Package
Un **package** es el **proyecto** en sí.
Se crea con `cargo new` y tiene un `Cargo.toml` que define **dependencias, metadata y configuración**. Es como el package.json 

##### Crate
Un **crate** es la **unidad compilable más pequeña en Rust**. Hay dos tipos de Crates
-  **Binary crate** → produce un ejecutable (`main.rs`).
-  **Library crate** → produce una librería (`lib.rs`).
```
mi_app/      (package)
 ├── Cargo.toml
 └── src/
      ├── main.rs   (binary crate)
      └── lib.rs    (library crate opcional
```
Si importás dependencias desde `Cargo.toml`, cada una es también un **crate**.  
Por ejemplo, `serde` es un crate.

###### Cuando usary binary o lib en source?

Binary
-  Se usa cuando querés que tu proyecto genere un **ejecutable**
-  Requiere una función de entrada `fn main()`
-  Ejemplo: aplicaciones CLI, servidores, programas que se ejecutan directamente

###### Library crate

-  Se usa cuando querés que tu proyecto sea una **librería reutilizable**
-  No tiene `main`, solo expone funciones, structs, módulos, etc
- Ejemplo: crates de terceros como `serde`, `tokio`, etc

Esto no se ejecuta directamente, sino que se **usa como dependencia** en otro crate

###### Usar ambos
Lo más común es tener **los dos juntos**
-  `main.rs` solo arma la aplicación y llama a funciones de la librería
-  `lib.rs` contiene la **lógica de negocio organizada en módulos**
- 
Tu lógica queda limpia y testeable en `lib.rs`.
Podés exponer la librería a otros proyectos.
`main.rs` solo maneja la inicialización.

---
##### Modules (mods)
-  Los **módulos** (`mod`) organizan el código dentro de un crate.
-  Sirven como **namespaces** y se reflejan en archivos/carpetas.
```
src/
 ├── main.rs
 ├── util.rs
 └── redes/
      ├── mod.rs
      └── cliente.rs
```
en main.rs
```rust
mod util;   // incluye util.rs como módulo
mod redes;  // incluye carpeta redes/mod.rs

fs main() {
	util::algunMetodo();
	redes::methods::otroMetodo();
}
```

##### Paths
Un **path** es la **ruta para acceder a una función, struct o módulo**
Puede ser
-  **Absoluto** → desde la raíz del crate (`crate::...`)
-  **Relativo** → desde el punto actual (`self::`, `super::`)
```rust
crate::util::saludar(); // absoluto
util::saludar();        // relativo
```

##### Use
`use` sirve para **traer un path al scope actual** y evitar escribir rutas largas
```rust
use crate::redes::cliente::conectar;
use crate::redes::{cliente, servidor}; //tambien podes importar varios a la vez

fn main() {
    conectar(); // ya no escribo redes::cliente::conectar()
}
```
`crate`es = a la raíz del crate actual (main.rs o lib.rs)

##### Resumiendo
1.  **Package** = todo el proyecto (tiene `Cargo.toml`)
2.  **Crates** = ejecutables o librerías dentro del package
3.  **Modules** = subdivisiones internas de un crate
4.  **Paths** = cómo referenciar ítems dentro de módulos o crates
5.  **use** = atajo para que los paths sean más cómodos de usar

---

#### Práctica
##### Cómo se define un modulo
```rust
mod utilidades {
	pub fn printName(nombre: &str) {
		println!("Hola {}", nombre);
	}
}

// en otro archivo
mod utilidades;

fn main() {
	let name = String::from("Ag");
	utilidades::printName(&name);
}
```
