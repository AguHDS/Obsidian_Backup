---
tags:
  - rust
  - basic
---
---
#### Ownership

En Rust **cada valor tiene un único dueño (owner)**.  
Cuando el owner sale de alcance (`scope, bloque`), el valor se libera de memoria automáticamente.

**move**: Cuando un valor pasa su valor a otro, se hace un **move**, es decir, se pasa el valor a otro owner.

Y si quiero usar el valor sin convertirme en su dueño? -> Aca entran las [[references]] y el concepto Borrowing (prestar un valor).

Basicamente, usas una referencia para referenciar tu valor y lo **prestas** sin perder tu adueñamiento

```rust
fn longitud(s: &String) -> usize {
    s.len()
}

fn main() {
    let nombre = String::from("Ana");
    let len = longitud(&nombre); // pasamos referencia
    println!("{} tiene {} caracteres", nombre, len);
}
```
✅ `nombre` sigue siendo válido después de la llamada.

---
#### Mutabilidad & borrowing

```rust
fn agregarString(string: &mut String) {
	string.push_str(" Mundo");
}

fn main() {
	let mut value = String::from("Hola");
	agregarString(&mut value);
	println!("{}", value);
}
```

Mutabilidad
-  `value` se declara como `mut`, lo que permite cambiar su contenido.
-  agregarString pide una referencia mutable, asi que se la pasamos
-  como pasamos una referencia a la función, value sigue estando disponible y podemos usarla en println!.
  
  
  Ultimo ejemplo
  
  ```rust
  fn main() {
    let mut name = String::new();
    std::io::stdin().read_line(&mut name).unwrap();

    let len = caracteres(&name);     // referencia inmutable
    add_text(&mut name);             // referencia mutable

    println!("Tu nombre es {} y tiene {} caracteres", name, len);
}

fn add_text(s: &mut String) {
    s.push_str(" (suscríbete!)");
}

fn caracteres(s: &String) -> usize {
    s.len()
}
  ```

Importante:
El `mut` al declarar la variable no obliga a que siempre pases referencias mutables.
Por ejemplo, name esta declarada como mutable (let mut name) pero cuando se usa como argumento en caracteres, la pasamos como &name, referencia inmutable.
Podemos decidir en que momento pasarla como mutable o inmutable.

En Rust, cuando pasas una referencia a una función, tú decides **si la referencia será mutable o inmutable**, independientemente de si la variable original es mutable: