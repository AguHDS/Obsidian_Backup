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

Las enums de rust son las mas parecidas a las que podemos encontrar en F# o en Haskell
Los enums son un tipo que puede tener **varios valores posibles**, llamados **variantes**:
En Rust, **cada variante de un enum puede llevar datos asociados**.  
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

Dato: 
En ese ultimo ejemplo se esta usando **pattern binding**, estas diciendo: _“dame lo que está adentro y asígnalo a esta variable”_. Por eso parece que Texto(string) espera un argumento como si fuera una función, pero no lo es.

---

#### Patterns

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



##### Que es Some

`Some` es **una variante de un `enum` llamado `Option`** que viene en la librería estándar de Rust
```rust
enum Options<T> {
	Some(T),
	None,
}
```

-  `Option<T>` representa un **valor que puede o no estar presente**.
-  `Some(valor)` → hay un valor y lo guarda.
-  `None` → no hay valor.

Es **una variante del enum `Option<T>`**, así que podés usarla para **crear valores** y también para **desestructurarlos**.

Para crear un valor Option
```rust
let x: Option<i32> = Some(5); // "x" contiene un valor
let y: Option<i32> = None;    // "y" no contiene valor

match x {
	Some(value) => println!("valor es {}", value),
	None => println!("No hay value"),
}
```
Esto es lo más común, pero no es obligatorio usar `match`

Podes pasar `Some(...)` como argumento a funciones que aceptan un `Option<T>`
```rust
fn procesar(n: Option<i32>) {
    match n {
        Some(v) => println!("Procesando {}", v),
        None => println!("Nada que procesar"),
    }
}

procesar(Some(10));
procesar(None);
```

Otro ejemplo

```rust
fn dividir(a: i32, b: i32) -> Option<i32> {
    if b == 0 {
        None
    } else {
        Some(a / b)
    }
}

let resultado = dividir(10, 2);

match resultado {
    Some(v) => println!("Resultado: {}", v),
    None => println!("División por cero"),
}
```
-  Rust **te fuerza a pensar en el caso de error** (`None`).
-  Si hubieras usado un `i32` normal, podrías olvidar manejar el error → bug silencioso.

##### Esto reemplaza el try-catch

En Rust **casi nunca se usan try-catch** como en js, porque el lenguaje **prefiere el manejo explícito de errores mediante tipos**

 Rust no tiene excepciones

-  No hay `throw`/`try-catch` para errores de runtime normales
-  Todas las funciones que pueden fallar **devuelven un `Result<T, E>`**
-  Esto obliga al programador a **manejar los errores explícitamente** en tiempo de compilación

```rust
fn dividir(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("División por cero"))
    } else {
        Ok(a / b)
    }
}

let resultado = dividir(10, 2);

match resultado {
    Ok(v) => println!("Resultado: {}", v),
    Err(e) => println!("Error: {}", e),
}
```
-  `Ok(v)` ↔ operación exitosa
-  `Err(e)` ↔ error
-  No hace falta try-catch; el compilador obliga a manejar ambas ramas.

Option vs Result
**`Option<T>`** → cuando el valor puede o no estar presente, pero no hay un “error” real
```rust
let usuario = usuarios.get(0); // devuelve Option<Usuario>

if let Some(u) = usuario {
    println!("Usuario encontrado: {}", u.nombre);
} else {
    println!("No hay usuarios");
}
```

**`Result<T, E>`** → cuando puede fallar y quieres describir **el error**:
```rust
fn abrir_archivo(nombre: &str) -> Result<String, String> { /* ... */ }
```

Dato de sintaxis
`if let` es solo **una forma más corta** cuando solo te interesa un patrón y no todos los casos (azucar sintáctiva)

El mismo `if let` es equivalente a:
```rust
match usuario {
    Some(u) => println!("Usuario encontrado: {}", u.nombre),
    None => (), // nada
}
```
