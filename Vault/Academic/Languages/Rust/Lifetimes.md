---
tags:
  - basic
  - rust
---
---

#### Que es el lifetime?

Los lifetimes son un **concepto general de Rust** que aparece siempre que haya **referencias prestadas** cuyo tiempo de vida tiene que estar claro
No se limitan a `struct`, sino también a funciones, métodos, traits y genéricos

Rust es muy estricto con la memoria: no quiere que tengas referencias que apunten a algo que ya fue destruido. Por ejemplo:
```rust
let r;

{
	let x = 5; // x se va a destruir al final del bloque
	r = &x; 
}
// r apunta a algo que ya no existe
```
Aquí Rust evitará que compile porque `r` tendría una referencia “colgante”
Los lifetimes ayudan a Rust a **garantizar seguridad de memoria sin un garbage collector**

```rust
fn mayor<'a>(x: &'a str, y: &str) -> &'a str {
    x
}

fn main() {
    let saludo = String::from("Hola");
    let despedida = String::from("Adiós");

    let r = mayor(&saludo, &despedida);
    println!("{}", r);
}
```

la funcion:
-  `'a` dice: _“la referencia que devuelvo no puede vivir más que `x`”_.
-  Rust necesita esto porque hay **más de una referencia de entrada** (`x` y `y`) y no puede inferir cuál debe “ganar” para la salida.

en main:
-  `saludo` y `despedida` son dos variables independientes.
-  La función devuelve **una referencia a `saludo`**, por eso el lifetime de la salida debe **estar ligado a `saludo`**.
-  Rust no podría adivinar esto sin el `'a`.

---

#### Lifetimes con genericos, cuando usarlos?

El caso típico es cuando **hay varias referencias de entrada**, y la salida depende de alguna de ellas. Por ejemplo:
```rust
Lifetime generico
fn ejemplo<'a>(a: &'a str, b: &str) -> &'a str {
    a
}

se usa en
fn main() {
    let s1 = String::from("Hola");
    let s2 = String::from("Mundo");

    let r = ejemplo(&s1, &s2);
    println!("{}", r); // imprime "Hola"
}

```
-  `<'a>` → declaramos un lifetime genérico `'a`.
-  `a: &'a str` → la referencia `a` vive al menos `'a`.
-  `b: &str` → otra referencia de entrada, con un lifetime **implícito distinto** (Rust no sabe cuál usar para la salida).
-  `-> &'a str` → especificamos que la referencia que devolvemos está ligada a `a`, no a `b`.

La referencia devuelta (`r`) vive **mientras `s1` viva**, no `s2`.

---

#### Cómo se cuando usar lifetimes?

##### **Guia mental**

Mi función o struct tiene varias referencias?

-  **Sí →** Necesitas considerar lifetimes
-  **No →** No necesitas lifetimes (solo tipos normales)

##### Es un struct

-  **Sí →** Si contiene referencias (`&T`) → **siempre necesitas declarar un lifetime genérico** para el struct
-  Ejemplo:
```rust
struct Persona<'a> {
	nombre: &'a str //referencia, necesita life-time
}
```
**No →** (struct con valores propios, no referencias) → No se necesitan lifetimes


##### Es una función

Recibe referencias?

-  **No →** No necesitas lifetimes
-  **Sí →** Pasa al siguiente paso

Devuelve referencias?

-  **No →** No necesitas lifetimes (Rust infiere que la referencia solo se usa dentro de la función)
-  **Sí →** Necesitas declarar lifetimes

##### Número de referencias de entrada

**Una sola referencia de entrada (`&T`)** → Rust puede **inferir** el lifetime automáticamente. No lo necesitas
```rust
fn longitud(s: &str) -> usize {
	s.len()
}
```

**Múltiples referencias de entrada (`&T`)** → Rust **no sabe cual inferir** para la salida → **necesitas un lifetime genérico explícito**

```rust
fn mayor<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

##### Es un método de struct que devuelve referencias internas?

-  **Sí →** El lifetime de retorno **debe estar ligado al lifetime del struct**....
```rust
impl<'a> Persona<'a> {
	fn get_nombre(&self) -> &'a str {
		self.nombre
	}
}
```


#### ✅ Reglas rápidas de memoria

1.  **Struct con referencias → siempre `'a` genérico**
2.  **Función con una referencia de parametro → usualmente Rust infiere**
3.  **Función con múltiples referencias de parametros → siempre `'a`, `'b`, etc**
4.  **Método que devuelve referencia interna de struct → `'a` ligado al struct**
5.  **Recuerda:** `'a` **no es un tipo**, es un marcador de duración de vida