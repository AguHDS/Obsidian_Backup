---
tags:
  - basic
  - rust
---
---

#### Como funciona el lifetime?

Los lifetimes son un **concepto general que aparece siempre que haya referencias prestadas** cuyo tiempo de vida tiene que estar claro.
En rust resolvemos el problema donde una referencia puede apuntar a un espacio en memoria que ya no existe (porque se liberó).
Esto se puede aplicar en cosas como `struct`, funciones, métodos, traits y genéricos

Rust es muy estricto con la memoria: no quiere que tengas referencias que apunten a algo que ya fue destruido. Por ejemplo:
```rust
let r;

{
	let x = 5; // x se va a destruir al final del bloque
	r = &x; 
}
// r apunta a algo que ya no existe (x)
```
Aquí Rust evitará que compile porque `r` tendría una referencia “colgante”
Los lifetimes ayudan a Rust a **garantizar seguridad de memoria sin un garbage collector**

```rust
fn comparar<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

fn main() {
    let a = String::from("hola");
	let b = String::from("hola2");
	let result = comparar(&a, &b);
	
    println!("El mas grande es:", result);
}
```

Aca rust va a compilar bien, porque el codigo cumple esta regla

**El resultado de la funcion tiene que tener el mismo tiempo de vida que las 
referencias a la que apunta**

Esto evita que rust tire error, porque aunque sea obvio que **a, b** van a seguir vivas, rust no lo sabe, esto hay que manejarlo manualmente de esta manera cuando por ejemplo, se tienen varias referencias.

Asi, si result apunta a la variable "a", sabe que no tiene que vivir mas que ella, y lo mismo con "b". Ya que siguen teniendo el ownership de los valores.

Si en la funcion se especificara que se retorna una referencia, y solo hubiera una referencia en la firma de la función, seria obvio a donde tiene que apuntar result, por eso no se necesitaria el lifetime ahí.

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


##### Hace falta usar move si es otro thread?

Otro ejemplo es cuando un closure va a ejecutarse en otro hilo, y necesitas mover el valor de argumento que le pasas porque al irse a otro hilo, necesita seguir existiendo ahí
```rust
let v = vec![1, 2, 3];
std::thread::spawn(move || {
    println!("{:?}", v); // se "lleva" v al hilo
});
```

Ejemplo típico en **Actix, Tokio o async-std**:
```rust
HttpServer::new(move || {
    App::new()
        .app_data(web::Data::new(pool.clone()))
})
```
El servidor mantiene ese closure (el | | {...}) vivo durante toda la vida de la aplicación, así que no podés depender de referencias prestadas → necesitas `move`.
El closure aca son los dos palitos "| |", no confundir con "or..." de otros lenguajes, en este contexto son para closures. Son como parentesis que no estan recibiendo parametros.

#### ✅ Reglas rápidas de memoria

1.  **Struct con referencias → siempre `'a` genérico**
2.  **Función con una referencia de parametro → usualmente Rust infiere**
3.  **Función con múltiples referencias de parametros → siempre `'a`, `'b`, etc**
4.  **Método que devuelve referencia interna de struct → `'a` ligado al struct**
5.  **Recuerda:** `'a` **no es un tipo**, es un marcador de duración de vida