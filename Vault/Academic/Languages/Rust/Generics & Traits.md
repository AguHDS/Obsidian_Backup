---
tags:
  - basic
  - rust
---
---

#### Generics (genericos)

Qué pasaria si por ejemplo una funcion tiene como parametro un tipo `&[i64]`, pero tambien queremos usar otros valores numeros o incluso string, u otros que vengan al caso? si crearamos una función para cada tipo seria un caos, es aca cuando entran los genericos

```rust
fn middle_element(list: &[char]) -> char {
	list[list.len()/2]
}
```
Esta función solo acepta valores tipo string, pero podemos hacerla mas dinámica usando genericos

Forma correcta:
```rust
fn middle_element<T: Copy>(list: &[T]) -> &T {
    &list[list.len() / 2]
}
```
De esta forma es mas dinámico, se usa de igual forma que los genericos en typescript

######  mas teoria aca

---

#### Traits
Son muy parecidas a las interfaces en TS.
Los traits estan muy relacionados con los generics, ya que podemos usarlos para ampliar los tipos de datos de los genericos

```rust
pub trait MakePrint {
	fn make_print(&self) -> String;
}

pub struct Persona {
	pub nombre: String,
	pub apellido: String,
	pub edad: i32
}

// Implementamos el trait para la struct persona
impl MakePrint for Persona {
	fn make_print(&self) -> String {
		format!("La pesrona es {}, su apellido es {} y tiene {} Años.", self.nombre, self.apellido, self.edad);
	}
}
```

---
##### Trait Copy & Clone, cuando usarlo

Cuando haces 
```rust
let a = 5;
let b = a;
```
en el caso de los enteros (`i32`, `u64`, etc.), **`a` sigue siendo válido** después de asignarlo a `b`. Eso es porque los enteros implementan automáticamente el trait **`Copy`** (y también `Clone`)

Pero en casos como por ejemplo strings:
```rust
let nombre = String::from("Agustin");
let b = nombre; // aquí s1 deja de ser válido (ownership se mueve a s2)
```
Rust no puede copiar automaticamente tipos como string, porque seria costoso

Y con structs, es neceario agregarle **Copy** y **Clone** con un atributo y

```rust
#[derive(Copy, Clone)]
struct Puntos {
	y: i32,
	b: i32.
}

fn main () {
	let a = Puntos { y: 12, b: 14 };
	let b = a;
	
	println!("p1.y = {}", a.y); // a sigue existiendo en el scope
}
```

Entonces, copy y clone son  necesarios cuando el tipo no lo implementa automaticamente

---

#### Traits and Generics

Podemos combinar los traits y genericos para extender el tipo que nuestro generico puede aceptar

```rust

// primero escribimos las cosas basicas
trait Resumible {
    fn resumen(&self) -> String;
}

struct Persona {
    nombre: String,
    edad: u32,
}

struct Producto {
    nombre: String,
    precio: f64,
}

impl Resumible for Persona {
    fn resumen(&self) -> String {
        format!("{} tiene {} años", self.nombre, self.edad)
    }
}

impl Resumible for Producto {
    fn resumen(&self) -> String {
        format!("{} cuesta ${}", self.nombre, self.precio)
    }
}

// Aca hacemos la combinación de trait y generico

fn imprimir_info<T: Resumible>(item: T) {
	println!("{}", item.resumen());
}

// Uso

fn main() {
	let p = Persona {
        nombre: "Ana".to_string(),
        edad: 30,
    };
    
    let pr = Producto {
        nombre: "Laptop".to_string(),
        precio: 1200.0,
    };
    
    imprimir_info(p);   // Funciona con Persona
    imprimir_info(pr);  // Funciona con Producto
}
```

Asi se usarían en combinación....