---
tags:
  - POO
  - basic
---
### 🔹 `extends`: Herencia de clases

En ts, cuando  usamos **extends** en otra, significa que **hereda** sus propiedades y métodos. Es decir, puede usar todo lo que ya tiene el "padre" o "base", y además agregar o cambiar cosas si quiere

```ts
class Animal {
  respirar() {
    console.log("respirando...");
  }
}

class Perro extends Animal {
  ladrar() {
    console.log("guau!");
  }
}
```

### ✅ `extends` en TypeScript se puede usar con:

1. **Clases** → para heredar comportamiento.
2. **Interfaces** → para extender otras interfaces (heredar estructura).
3. **Tipos (`types`)** → también puede combinarse con interfaces o tipos usando `extends` dentro de condiciones (`extends` condicional)

Ejemplo con type con extends condicional
```ts
function imprimirNombre<T extends InterfaceConNombre>(obj: T) {
console.log(obj.nombre)
}
//asumimos que InterfaceConNombre es { nombre: string }

```
Esto significa que el parámetro `T` **debe tener al menos una propiedad `nombre`**. Es como decir: "puede ser cualquier tipo, mientras tenga esto".

---

### 🔹 `implements`: Aplicar una interfaz

`implements` no hereda comportamiento como `extends`, sino que **obliga a la clase a tener cierta estructura**, como un contrato.
```ts
interface Volador {
  volar(): void;
}

class Pajaro implements Volador {
  volar() {
    console.log("Volando alto...");
  }
}
//retorna console.log, osea: un void, como quiere la interface.
```
**obliga a que la clase cumpla con la interfaz tal cual está definida**, **ni más ni menos** en cuanto a **métodos y propiedades requeridas**. 

En **TypeScript**, `implements` **solo funciona con clases que implementan interfaces o tipos de objetos**, **no entre objetos directamente**.

✅ ¿Qué sí se puede hacer?
```ts
interface Computadora {
	precio: number;
	encender(): void;
}

class Pc implements Computadora {
	private precio: number;

	constructor(precio) {
		this.precio = precio;
	}

	public encender() {
		console.log(`La pc te costó ${this.precio}`)
		console.log("Encendiendo PC...");
	}
}

const myPc = new Pc(250);
myPc.encender();
```
TypeScript verifica que Pc tenga todas las propiedades y métodos definidos en `Computadora`

---

### 🔹 `super`: Llamar al constructor o métodos del padre

Cuando una clase extiende a otra, podés usar `super` dentro del constructor para **inicializar** la parte heredada del objeto. No es que "clone" el objeto, sino que se asegura de que la parte del padre esté correctamente preparada antes de hacer cosas específicas del hijo.

```ts
class Animal {
  nombre: string;
  constructor(nombre: string) {
    this.nombre = nombre;
  }
}

class Perro extends Animal {
  constructor(nombre: string) {
   // llama al constructor de Animal
    super(nombre);
  }
}

```
**cuando usás `super`, podés agregar cosas nuevas en la clase hija sin problema.**

`super()` simplemente se encarga de **inicializar la parte que heredas de la clase padre**. Después de eso, sos libre de agregar tus propias propiedades, métodos o lógica.

```ts
class Animal {
  nombre: string;

  constructor(nombre: string) {
    this.nombre = nombre;
  }

  hacerSonido() {
    console.log("Hace algún sonido");
  }
}

class Perro extends Animal {
  raza: string;

  constructor(nombre: string, raza: string) {
    super(nombre); // inicializa 'nombre' usando el constructor del padre
    this.raza = raza; // propiedad nueva de la clase hija
  }

  ladrar() {
    console.log("Guau!");
  }
}
```