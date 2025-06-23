---
tags:
  - POO
  - medium
  - frontend
  - backend
---
### ✅ ¿Qué es inyección de dependencias?

Es un **principio de diseño** donde una clase **recibe (le "inyectan") sus dependencias desde afuera**, en lugar de **crearlas por sí misma**.

#### ¿Qué es una dependencia?

Cualquier cosa que un objeto necesita para funcionar:

-  Una clase (como un repositorio, servicio, etc.)
-  Una función
-  Una API

---

### Ejemplo

❌ Sin inyección de dependencias

```ts
class UserService {
  private db = new MySQLDatabase(); // ← Acoplado (mala práctica para escalar)

  getUser(id: number) {
    return this.db.findUser(id);
  }
}
```
Aquí, `UserService` **crea su propia dependencia** (`MySQLDatabase`). Esto **rompe la flexibilidad**, ya que no puedes cambiar fácilmente la base de datos.

✅ Con inyección de dependencias
```ts
interface Database {
  findUser(id: number): User;
}

class UserService {
  constructor(private db: Database) {}

  getUser(id: number) {
    return this.db.findUser(id);
  }
}
```
Ahora `UserService` **no sabe qué tipo de base de datos está usando**. Puede ser `MySQLDatabase`, `MongoDatabase`, un mock, etc. Eso es inyección de dependencias.