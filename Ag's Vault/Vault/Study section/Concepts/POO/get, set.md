---
tags:
  - POO
  - basic
  - get
  - set
  - getter
  - setter
---
---

**`get y set`** son  accesores especiales.  
Están diseñados para **comportarse como propiedades** pero ejecutar lógica interna como métodos. Se usan para modificar y leer propiedades de una clase

Ejemplo:
```ts
class Persona {
  private _edad: number = 0;

  get edad(): number {
    return this._edad;
  }

  set edad(nuevaEdad: number) {
    if (nuevaEdad >= 0) {
      this._edad = nuevaEdad;
    } else {
      console.log("Edad inválida");
    }
  }
}

const p = new Persona();
p.edad = 25; // 🔁 Se ejecuta el 'set'
console.log(p.edad); // 🔁 Se ejecuta el 'get'
// p._edad ❌ No se puede acceder directamente (es private), el "_" le da un nombre distinto a la propiedad, usar p.edad y p._edad no es lo mismo.

```
Acá, typescript detecta que edad puede ser un setter o un getter, y dependiendo de si igualas un valor o accedes a la propiedad, ejecuta uno o el otro. Si ejecuta el get, muestra el dato retornado, si ejecuta el set, pasa el valor igualado como argumento al parametro.

----

## 🧩 ¿Por qué usar `get` y `set`?

|Caso práctico|¿Cómo ayuda?|
|---|---|
|Validar valores antes de asignarlos|`set` permite controlar qué entra|
|Mostrar datos calculados|`get` puede devolver resultados dinámicos|
|Encapsular propiedades privadas|Sin exponerlas directamente|
|Mantener consistencia de la lógica|El acceso siempre pasa por el mismo punto|

🎯 Ejemplo real: propiedad calculada
```ts
class Rectangulo {
  constructor(private ancho: number, private alto: number) {}

  get area(): number {
    return this.ancho * this.alto;
  }
}

const r = new Rectangulo(4, 5);
console.log(r.area); // ✅ 20 (se calcula automáticamente)

```
`No estás llamando r.area(), sino accediendo como si fuera una propiedad, pero en realidad se está ejecutando una función detrás.`

---

### 🧠 Duda típica: un método y un get/set, hacen lo mismo?

**Sí, funcionalmente pueden hacer lo mismo**, porque ambos pueden asignar valores y ejecutar lógica personalizada

## 🎯 ¿Entonces, cual usar ?

| Criterio                       | Usa `set/get`                | Usa método (`method()`)       |     |
| ------------------------------ | ---------------------------- | ----------------------------- | --- |
| Estilo moderno y natural       | ✅ Sí                         | ❌ Menos elegante              |     |
| Querés tratarlo como propiedad | ✅ Sí                         | ❌ No parece propiedad         |     |
| Querés claridad explícita      | ❌ Puede esconder lógica      | ✅ Más claro que es una acción |     |
| Es parte de una API pública    | ✅ Más amigable para usuarios | ✅ Aceptable también           | .   |

## 🧩 ¿Qué se prefiere?

- En código **orientado a objetos moderno (TypeScript, C#, etc.)**, se prefiere usar `get`/`set` **cuando estás modelando propiedades** que parecen "naturales" (edad, nombre, email, etc.).

- Si la acción es más compleja o **implica más que una simple asignación**, es mejor usar un **método


------

#### 🛑 Reglas importantes

-  Un `get` debe **devolver algo** (`return`)
-  Un `set` debe **recibir un parámetro**
-  Se tienen que usar desde un objeto instanciado, no desde la misma clase
-  No podés tener una propiedad `get` o `set` con el mismo nombre que otra propiedad pública