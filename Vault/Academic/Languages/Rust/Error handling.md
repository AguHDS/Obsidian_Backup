---
tags:
  - basic
  - rust
---
---

#### Manejo de errores en Rust con enum

En rust, no existe **null**, se usa es enum **Option** y **Result**
(ver enums en ([[Struct, enums, patterns]]))

###### Result<T, E>

**Se usa cuando hay éxito o error con información**
```rust
enum Result<T, E> {
	Ok(T),
	Err(E),
}

fn dividir(a: i32, b: i32) -> Result<String, i32> {
	if b == 0 {
		Err("No se puede dividir por cero".to_string());
	} else {
		Ok(a / b);
	}
}

fn main() {
	let result = dividir(2, 0); {
		Ok(resultado) => println!("Resultado: {}", resultado),
        Err(error) => println!("Error: {}", error),
	}
}
```
-  Rust **fuerza a manejar los errores**: no puedes simplemente ignorarlos
-  Esto **evita muchos crashes por null/undefined como en otros lenguajes**

###### Option< T >

**Se usa cuando hay ausencia de valor, no error**
```rust
fn buscar_elemento(v: &[i32], n: i32) -> Option<usize> {
    v.iter().position(|&x| x == n)
}

fn main() {
    match buscar_elemento(&[1,2,3], 2) {
        Some(pos) => println!("Encontrado en la posición {}", pos),
        None => println!("No encontrado"),
    }
}
```
-  `Option` es un enum: `Some(T)` o `None`
-  Se usa cuando **no hay un error “real”**, solo ausencia de valor

Nota:
usar **null** en programación suele ser peligroso, porque puedes estar accediendo a un valor que no existe y eso no se detecta en tiempo de compilación. Esto lleva a errores inesperados cuando el programa se ejecuta. Rust entiende esto y resuelve ese problema.

---

#### Panic
`panic!` es una **macro** que Rust usa cuando ocurre un **error irrecuperable** en tiempo de ejecución.  
Cuando se ejecuta:

-  Se imprime un mensaje de error
-  Se limpian los recursos (drop de variables)

El programa se detiene completamente

Cuando ocurre?
-  **Cuando lo invocás explícitamente** con `panic!()`
- **Cuando una función de la librería estándar falla internamente**

##### Cuando usar panic?
-  Si encontraste una situación en la que **no tiene sentido continuar**
-  Cuando algo que debería ser **siempre verdadero** se rompe
-  Si algo debería ser **imposible de pasar** y pasa, es mejor cortar la ejecución


##### Cuándo **no usar `panic!`**
En **errores esperables** que el usuario puede resolver (archivo faltante, conexión caída, input inválido). Ahí se debe usar `Result<T, E>`.

##### Resumen práctico

-  **Errores esperables/recuperables** → `Result` / `Option`
-  **Errores imposibles de manejar o bugs** → `panic!`
-  En producción, se busca minimizar `panic!` salvo para casos donde continuar sería peligroso o incorrecto