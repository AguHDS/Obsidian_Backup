---
tags:
  - rust
  - basic
---
---

#### dyn Trait

Un **trait object** (`dyn Trait`) en Rust es una forma de **despacho dinámico**: permite trabajar con valores cuyo **tipo concreto no se conoce en tiempo de compilación**, siempre que implementen un determinado **trait**.

Se usa `dyn Trait` cuando **no importa el tipo concreto**, pero **sí importa la interfaz (el trait) que implementa**, es decir, los métodos definidos en ese trait.

IMPORTANTE:
Interfaz (trait) y tipo **NO** son lo mismo. Esto puede confundir si se viene de otros lenguajes como C#, Java, Typescript, etc...

El **Tipo** es la implementación, como una `struct`, un `enum`, etc.
```rust
struct perro;
struct gato;
```

El trait es la interfaz, el conjunto de metodos:
```rust
trait Animal {
	fn hablar(&self);
}
```

Los tipos implementan traits:
```rust
impl Animal for perro {
	fn hablar(&self) {
		println!("hi");
	}
}
```

Entonces, el dyn Trait se implementa asi:

```rust
fn hacer_hablar(animal: dyn Animal) {
	animal.hablar();
}
```


le estás diciendo al compilador:

> “No sé si me van a pasar un `Perro`, un `Gato`, o cualquier otra cosa.  
> Lo único que necesito saber es que **implementa el trait `Animal`**,  
> así puedo llamar a `hablar()`.”

En este ejemplo de mi bot:
```rust
let task_repo: Arc<dyn TaskRepository> = Arc::new(JsonTaskRepository::new("./data/tasks.json"));
```

“`task_repo` es un **puntero compartido** (`Arc`) a **algún objeto que implemente el trait `TaskRepository`**. No necesitamos saber **el tipo concreto** (`JsonTaskRepository` u otro), pero sí sabemos que **cumple la interfaz de `TaskRepository`** (es decir, implementa todos los métodos del trait).”