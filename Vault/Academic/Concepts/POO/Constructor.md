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


---

### Propiedad de parámetro
En una clase podemos declarar la propiedad directamente en el constructor
```ts
class EmailService {
  constructor(private mailer: MailerClient) {}

  sendWelcomeEmail(user: User) {
    this.mailer.send(user.email, 'Welcome!');
  }
}
```

✅ Cuando usarla:

-  Cuando tenés **1 o pocas dependencias**.
-  Cuando las propiedades no necesitan lógica extra.
-  Para **servicios, controladores o componentes simples**.
-  Si querés mantener el código **conciso y limpio**.

❌ Cuando NO conviene usarla

-  Validar o transformar el valor antes de asignarlo.
-  Usar decoradores (por ejemplo, en NestJS).
-  Documentar bien cada propiedad (comentarios, tipos más complejos).
-  Tener propiedades opcionales o valores por defecto.
```ts
class ComplexService {
  private db: Database;

  constructor(db: Database) {
    if (!db.isConnected()) {
      throw new Error('Database not connected');
    }
    this.db = db;
  }
}
```