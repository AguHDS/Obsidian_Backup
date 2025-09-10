---
tags:
  - basic
  - rust
---
---

#### 1. El operador `.` (punto)

Se usa para **instancias** de structs, enums o tipos

📌 Casos de uso

-  Acceder a **campos** de una instancia
```rust
struct Usuario { id: u32, nombre: String }
let u = Usuario { id: 1, nombre: String::from("Ana") };
println!("{}", u.id); // campo

//- Acceder a índices en tuple structs o tuplas:
struct Color(u8, u8, u8);
let c = Color(255, 128, 64);
println!("{}", c.0); // primer campo

//- Llamar a métodos de instancia:
impl Usuario {
    fn saludar(&self) {
        println!("Hola, soy {}", self.nombre);
    }
}
u.saludar(); // método
```


#### 2. El operador `::` (dos puntos)
Se usa para **nombres asociados a un tipo o un módulo**, no a una instancia

 📌 Casos de uso

```rust
//-  Llamar a **funciones asociadas** (ej: constructores estilo `new`):
impl Usuario {
    fn nuevo(id: u32, nombre: String) -> Self {
        Self { id, nombre }
    }
}
let u = Usuario::nuevo(1, String::from("Ana"));

//- Acceder a constantes/estáticos asociados
struct Config;
impl Config {
    const VERSION: &'static str = "1.0.0";
}
println!("{}", Config::VERSION);

//- Acceder a módulos o namespaces
mod util {
    pub fn imprimir() { println!("Hola!"); }
}
util::imprimir();

//Crear **variantes de un enum:
enum Mensaje {
    Texto(String),
    Numero(i32),
}
let m = Mensaje::Texto(String::from("Hola"));


```


#### 3. Comparación rápida

| Operador | Contexto                        | Ejemplo                                                 |
| -------- | ------------------------------- | ------------------------------------------------------- |
| `.`      | Campos/métodos de **instancia** | `u.nombre`, `u.saludar()`, `color.0`                    |
| `::`     | Elementos de **tipo/módulo**    | `Usuario::nuevo()`, `Config::VERSION`, `Mensaje::Texto` |