---
tags:
  - rust
  - basic
---
---

#### Structs

-  Es una estructura de datos, como un objeto
-  Define claves y valores
-  Puede tener metodos asociados (con impl, desde fuera), no se le pueden poner metodos en su creación


 Por qué Rust quiere que implementes metodos por fuera?

-  **Claridad**: una `struct` define solo datos; el `impl` agrega el comportamiento
-  **Flexibilidad**: podés tener **varios bloques `impl`** para la misma `struct`, incluso en distintos archivos o crates

```rust
struct User {
	id: u32,
	nombre: String,
}

impl User {
	fn hi(&self) {
		println!("Hola, soy {}", self.nombre);
	}
}

fn main() {
	let user = Usuario { id: 1, nombre: String::from("Ana") }
	user.hi(); // El parametro &self, no se escribe, eso lo está representando user.hi
}
```

**User es una implementación real con memoria, ocupa espacio en el stack/heap, a diferencia de las interfaces**

En Rust, el `impl` no define “constructores” como en otros lenguajes, pero **vos mismo podés crear funciones asociadas** que actúan como constructores.

Por ejemplo, en este caso, User::nuevo(...) cumple el papel de constructor

```rust
impl User {
	fn nuevo(id: u32, nombre: String) -> Self {
		Self { id, nombre }
	}
}
```
`Usuario::nuevo(...)` es como un **constructor estático**, porque devuelve una instancia lista para usar.

En **Rust no existe la palabra clave `constructor`** como en TypeScript, Java o C#. La convención es: 
-  **Definir una `struct`** para representar los datos
-  **Agregar funciones asociadas dentro de `impl`** que construyen instancias, como vimos

Note:
. El operador "." se usa para acceder a metodos sobre una instancia de objeto ya creada, sino, se usa el "::" **para acceder a funciones o constantes asociadas a un tipo**, un módulo o un trait. No necesita una instancia porque el acceso se hace en el tipo.

. En rust usamos self, no this


---

#### Enums

Se usan para el [[Error handling]]
Los enums tienen variantes, y esas variantes pueden tener datos que pueden ser cualquier cosa siempre que cumplan con su tipo.
La sintaxis cambia según cómo quieras guardar esos datos

1. Variantes sin datos (son simples etiquetas.)
```rust
enum Direccion {
	Norte,
	Sur,
	Este,
	Oeste
}

fn main() {
	let dir = Direccion::Norte;
	
	match dir {
		Direccion::Norte => println!("Esto se va a printear");
		Direccion::Norte => println!("Esto no se va a printear");
		Direccion::Norte => println!("Esto no se va a printear");
		Direccion::Norte => println!("Esto no se va a printear");
	}
}
```

`enum` con datos asociados
2. Variantes **tipo tupla**
```rust
enum Mensaje {
	Texto(String),
	Numero(i32),
	Saludo { nombre: String }, // variante tipo Struct
}

fn main() {
	// Aca editamos las instancias, no cambia le molde Mensaje real
	let msj = Mensaje::Texto(String::from("un mensaje"));
	let numero = Mensaje::Numero(42);
	let saludo = Mensaje::Saludo{nombre: String::from("algo")};
	
	match msj {
		Mensaje::Texto(text) => println("Texto es {}", text);
	}
}
```
Los paréntesis indican que esa variante se comporta como una **tupla**, o sea, una “cajita” que guarda valores.

Entonces:
-  Si usás **paréntesis** → es como una **tupla**: el orden importa, no hay nombres.
-  Si usás **llaves** `{}` → es como un **struct**: cada campo tiene nombre.
-  Si no usás nada → es solo una **etiqueta sin datos**.

podemos desestructurar un valor con **if let**. Es una especie de atajo más conciso que usar `match` cuando solo te importa **un solo caso**.
```rust
let any_num: Option<i8> = Some(24);

if let Some(i: 18) = any_num() {
	println!("Numero es: {}", i);
} else {
	println!("No hay ningún valor");
}
```

Dato: 
En ese ultimo ejemplo se esta usando **pattern binding**, estas diciendo: _“dame lo que está adentro y asígnalo a esta variable”_. Por eso parece que Texto(string) espera un argumento como si fuera una función, pero no lo es.
Las enums de rust son las mas parecidas a las que podemos encontrar en F# o en Haskell

---

#### Patterns (match)

Un **pattern (patrón)** en Rust es una **forma de desestructurar valores**.  
Lo usás para _decirle al compilador qué forma tiene un valor y qué partes te interesan extraer_.

Se usan en `match`, `if let`, `while let`, `for`, y también en `let`. Como vimos arriba en el ultimo ejemplo

1. Pattern básico: coincidencia literal

```rust
let x = 4;

match x {
	1 => println!("no coincide");
	2 => println!("no coincide");
	3 => println!("no coincide");
	4 => println!("coincide"),
	_=> println("Done"); // comodin, es como el default y se ejecuta si no hubo ninguna coincidencia
}
```

2. Patterns con variables (binding)

Podes capturar valores en una varaible
```rust
let x = Some(5);

match x {
	Some(value) => println!("Tengo {}", value), //binding
	None => println!("Nada");
}
```

