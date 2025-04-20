---
tags:
  - POO
  - basic
---
## 🏗️ ¿Qué es un constructor?

Un **constructor** es una función especial dentro de una [[Class]] que se ejecuta automáticamente cuando se crea un nuevo objeto con la palabra clave `new`

> Piensa en el constructor como la **receta de inicialización** de un objeto:  
> “Cuando te creen, así es como debes configurarte”

---
### 🧠 ¿Qué hace exactamente?

Toma los **parámetros recibidos** y los asigna a las **propiedades internas del nuevo objeto**:

```ts
constructor(newNombre: string, newEdad: number) {
  this.nombre = newNombre;
  this.edad = newEdad;
}

```

Este paso mentalmente significa:
```ts
this.nombre = newNombre;

```
***A la propiedad 'nombre' del objeto nuevo que estoy creando (this), asígnale el valor recibido como argumento.

- newNombre y newEdad son parametros temporales del constructor. Solo existen en el constructor.
- This.nombre y this.edad de la izquierda, son las propiedades que se declaran en la clase mas arriba, con los modificadores de acceso
- En el nuevo objeto creado, accedemos con a las propiedades con newObjet.nombre o newObject.edad

---
### ✨ Ejemplo completo

```ts
class Civil {
  nombre: string;
  edad: number;

  constructor(newNombre: string, newEdad: number) {
    this.nombre = newNombre;
    this.edad = newEdad;
  }
}

const newCivil = new Civil("Agustin", 24);
```
### 🔍 ¿Qué pasa internamente?

1. Se crea un nuevo objeto `newCivil` del tipo `Civil`.
2. El constructor recibe `"Agustin"` y `24`.
3. Se asignan los valores:
```ts
    - `this.nombre = "Agustin"`
    - `this.edad = 24`
```
4. Resultado: `newCivil` ahora tiene:
```ts
{
  nombre: "Agustin",
  edad: 24
}
```