---
tags:
  - Arquitecture_Pattern
  - sub_principal
---

### ❗ Antes de empezar, hay que saber:

Que son:
-  Layers (capas)
-  Entities
-  Repositories
-  Domain
-  useCases
-  Infraestructure
-  Adapters
-  Ports

---
#### Layers (Capas)

Qué son las capas? son las formas en la que se organiza el codigo por responsabilidades.
En código, una “**capa**” puede ser simplemente un **conjunto de archivos, clases o funciones** que:
-  Se dedican a una tarea específica
-  Interactúan con otras capas a través de interfaces claras

📚 Ejemplo práctico
Supongamos que estás haciendo una app para registrar usuarios. Puedes tener estas capas:

| Capa (Responsabilidad)      | Qué hace                                                 | Ejemplo de archivos                           |
| --------------------------- | -------------------------------------------------------- | --------------------------------------------- |
| **Dominio (Core)**          | Lógica pura: ¿cómo se registra un usuario?               | `user.py`, `register_user.py`                 |
| **Aplicación (Use Cases)**  | Coordina tareas: "cuando llega una petición, haz esto"   | `user_service.py`                             |
| **Infraestructura**         | Conecta con el mundo real: base de datos, APIs, etc.     | `user_repository_postgres.py`, `email_api.py` |
| **Interfaz (UI / Entrada)** | Lo que el usuario o sistema externo usa: HTTP, CLI, etc. | `routes.py`, `cli_commands.py`                |

 📦 ¿Y las carpetas?
**No es obligatorio** que cada capa esté en su carpeta, pero **ayuda muchísimo**. Ejemplo:

```ts
/project
├── domain/         ← capa de Lógica pura
├── application/    ← capa Casos de uso / servicios
├── infrastructure/ ← capa DB, APIs, cosas externas
└── interface/      ← capa Entradas como HTTP, CLI
```
 
 🛠 ¿Por qué es útil usar capas?
- Puedes **cambiar la base de datos** sin tocar la lógica
- Puedes **testear la lógica** sin conectarte a nada externo
- Puedes **leer el código y entender qué hace cada parte**

---
#### Entities

Para que algo sea una entidad, tiene que cumplir con requisitos conceptuales.
-  Tener identidad propia, como un **id**, que permanece constante
-  Ese ID representa a la entidad **independientemente de sus atributos**.

📦 _Ejemplo:_ 
Un usuario (`User`) puede cambiar de nombre, correo, contraseña… pero sigue siendo el mismo usuario porque su identidad (`userId`) no cambia. Puede ser una clase, un objeto.

-  Debe contener **lógica de negocio** relacionada consigo misma.
-  No es un simple “contenedor de datos” (como un DTO), sino que **hace cosas**, con sentido para su contexto.

📦 _Ejemplo:_  
```ts
class Order {
  constructor(public id: string, public total: number, public paid: boolean = false) {}

  pay() {
    if (this.paid) throw new Error("La orden ya fue pagada");
    this.paid = true;
  }
}
```

- Esta en el centro del dominio
- Las entidades forman parte del **modelo de dominio**: representan cosas "con peso" en el negocio.
-  No son solo datos flotantes. Son el **vocabulario real del problema**.
    

📦 _Ejemplo (negocio de reservas):_
- `Reserva`, `Cliente`, `Habitación`, `Pago` son entidades.
- `ReservaDTO`, `RequestBody`, `UserModel` no lo son (estos están en otras capas).

 ❌ ¿Qué NO es una entidad?
- Un simple objeto con datos (`interface`, `DTO`, `request body`)
- Un resultado de cálculo puntual
- Una clase que representa lógica de aplicación pero **sin estado persistente**

🧠 Tip mental:
> **Una entidad representa "algo del mundo real"** dentro de tu negocio.  
> Si tu sistema modela una tienda, tus entidades serán `Product`, `Cart`, `User`, `Order`.

---
#### Repositories

Es una interface que muestra el contrato/estructura, pero no la logica.
Los **repositories solo deberían contener métodos públicos que definen operaciones de acceso a entidades**, y **no deberían tener estado propio ni lógica adicional**, que no esté relacionada con la persistencia, ni siquiera propiedades.

Los repositorios:
- Expone solo opciones relevantes para el dominio
- Solo métodos que **tengan sentido para las entidades del dominio**: buscar, guardar, eliminar, etc.

Ejemplo
```ts
interface ProductRepository {
  findById(id: string): Promise<Product | null>;
  save(product: Product): Promise<void>;
  deleteById(id: string): Promise<void>;
}
```
 
 **Es una interfaz definida en el Dominio**
- El contrato (interfaz) **vive en el dominio**, no en la base de datos ni en infraestructura
- Esto permite cambiar la implementación sin tocar casos de uso

📦 _Ejemplo:_  
En `/domain/repositories/ProductRepository.ts` defines la interfaz
En `/infrastructure/db/mongo/MongoProductRepository.ts` haces la implementación

**No contiene lógica de negocio**
-  Solo **acceso a datos** (consultar, persistir, eliminar).    
-  No debería validar reglas, aplicar descuentos, ni decidir si algo está aprobado.

```ts
// Esto está mal: lógica de negocio en repositorio
if (product.stock > 0) { product.applyDiscount(); }
```
 
 **Oculta los detalles de persistencia**

- El código que llama al repositorio **no necesita saber si usas Mongo, PostgreSQL, archivos, Redis...**    
- Solo conoce los métodos definidos en la interfaz.

```ts
// En un caso de uso
const product = await productRepo.findById("123");
// No importa si por detrás usa SQL o JSON plano
```


❌ ¿Qué **NO** debe hacer un Repository?

| ❌ Error común                          | Por qué está mal                                                                         |
| -------------------------------------- | ---------------------------------------------------------------------------------------- |
| Usar queries SQL crudas en toda la app | Viola el principio de abstracción                                                        |
| Tener lógica de negocio                | Debe delegarla al dominio o a los casos de uso                                           |
| Ser dependiente de un framework        | La interfaz no debe importar ni Sequelize, ni Prisma, etc.                               |
| Devolver datos crudos del ORM          | Debe mapear a entidades del dominio (no devolver objetos Mongo o Sequelize directamente) |

---
#### Domain

El corazón del sistema. Es la carpeta en la están las **entidades** y las **reglas de negocio puras**, sin importar cómo se presentan o almacenan.

**Contiene:**
- **Entidades**: clases con reglas internas.
- **Interfaces de repositorios**: contratos para guardar/cargar entidades (sin implementación).
Ejemplo:

/domain
├── entities
│   └── Product.ts
└── repositories
    └── ProductRepository.ts
```ts
//entities/products.ts
export class Product {
  constructor(public id: string, public name: string, public price: number) {}

  applyDiscount(percent: number) {
    this.price -= this.price * (percent / 100);
  }
}
```

```ts
//repositories/productRepository.ts
import { Product } from "../entities/Product";

export interface ProductRepository {
  save(product: Product): Promise<void>;
  findById(id: string): Promise<Product | null>;
}
```

---
#### useCases (casos de uso)
Lógica que **usa las entidades para cumplir acciones concretas del sistema**. No sabe nada de frameworks, bases de datos ni UI.

Ejemplo
/src
├── application
│   └── use_cases
│       └── ApplyDiscountUseCase.ts
```ts
//use_cases/applyDiscountUseCase.ts
class ApplyDiscountUseCase {
  constructor(private productRepo: ProductRepository) {}

  async execute(productId: string, discount: number) {
    const product = await this.productRepo.findById(productId);
    if (!product) throw new Error("Producto no encontrado");

    product.applyDiscount(discount);
    await this.productRepo.save(product);
  }
}
```

Donde esta la lógica de findById? -> **ProductRepository.ts** aqui esta la el contrato que dice que se va a hacer, pero no como
**Entonces, donde se hace la lógica?**
La implementación concreta se hace en los **adapters**/controllers. Ellos lo importan y los usan a los useCases
Y luego, se pone el controlador en la route, como se hace siempre.

---

#### Infraestructure

 ✅ **Resumen directo:**
- **`infrastructure/` es la carpeta**  
    que **suele contener** la implementación concreta de cosas como bases de datos, servicios externos, etc.
- **Los archivos dentro de `infrastructure/` normalmente son los adaptadores**  
    porque están **implementando interfaces del dominio**, usando herramientas del mundo real (Express, Mongo, PostgreSQL, etc.).


🔁 Ejemplo para fijarlo mejor:
Supongamos que en tu dominio definiste esto:
```ts
import { Product } from ../domain/entities/Product.ts;

// /domain/repositories/ProductRepository.ts
export interface ProductRepository {
  findById(id: string): Promise<Product | null>;
}
```
Ahora vas a implementarlo **usando MongoDB**.
```ts
// /infrastructure/repositories/MongoProductRepository.ts
import { ProductRepository } from "../../domain/repositories/ProductRepository";

export class MongoProductRepository implements ProductRepository {
  async findById(id: string): Promise<Product | null> {
    const doc = await mongo.collection("products").findOne({ _id: id });
    return doc ? new Product(doc._id, doc.name, doc.price) : null;
  }
}
```
Este archivo **MongoProductRepository.ts** es un adaptador, porque:
-  Implementa una interfaz del dominio (`ProductRepository`)
-  Traduce entre el **formato del dominio** y el **formato del mundo real (Mongo)**

Este adaptador **vive dentro de la carpeta `infrastructure/`**, por convención y porque depende de herramientas externas.

---
#### Adapters / Controllers

Un adaptador es un archivo que **recibe datos de una capa** (por ejemplo, la base de datos o la API) y los **convierte al formato que otra capa necesita** (como el dominio o el frontend).  
Su trabajo es conectar dos capas que **no se entienden directamente entre sí**.

Pero no todos los adaptadores son iguales. Hay **tres tipos principales** en Clean Architecture:

 🟠 1. **Adaptadores de entrada** (Input Adapters)
> Transforman el input del mundo externo en una llamada a un caso de uso.

```ts
// Express (entrada por HTTP)
app.post("/login", async (req, res) => {
  const useCase = new LoginUseCase(...);
  const result = await useCase.execute(req.body);
  res.json(result);
});
```
Este **controller** es un **adaptador de entrada**.

 🔵 2. **Adaptadores de salida** (Output Adapters)
 > Implementan interfaces del dominio para hablar con sistemas externos (DB, API, etc...)
 
 ```ts
import { User } from "../../domain/entities/User";
import { UserRepository } from "../../domain/repositories/UserRepository.ts"

class MongoUserRepository implements UserRepository {
  async findById(id: string) {
    const doc = await db.collection("users").findOne({ _id: id });
    return new User(doc._id, doc.email); <- 
	//constructor(public id: string, public email: string) {} <- el constructor en       User se veria algo como eso
  }
}
```
Este archivo **es un adaptador de salida**, porque **traduce** entre la **interfaz del dominio** y **MongoDB**.
Aca vemos que findById es el **método que hace la logica de negocio a hacia la db**, cumpliendo el contrato que pide la interfaz en UserRepository

Los adapters suelen ser llamados controllers, si manejan peticiones http. Ej: userController.ts

 🎯 Regla práctica para nombrar

| Tipo de archivo               | Sufijo recomendado  | Ubicación típica            |
| ----------------------------- | ------------------- | --------------------------- |
| Controlador HTTP              | `Controller.ts`     | `/interfaces/controllers/`  |
| Repositorio real (DB)         | `Repository.ts`     | `/infrastructure/db/`       |
| Servicio externo (API, email) | `ServiceAdapter.ts` | `/infrastructure/external/` |
| Cliente de API externa        | `Client.ts`         | `/infrastructure/api/`      |

---

#### Ports (in / out)
Concepto usado en arquitectura Hexagonal

Ports in (de entrada)
También llamados **puertos de entrada** o _driving ports_.

**Que hacen?**
Definen las **operaciones que el sistema ofrece** al mundo exterior (casos de uso).

**Quién los implementa?**
Los **casos de uso** o servicios de aplicación. Son clases que ejecutan la lógica de negocio.

**Quién los llama?**
Los **adaptadores de entrada** como:
-  Controladores HTTP (REST, GraphQL)
-  Interfaces de línea de comandos (CLI)    
-  Event listeners
-  Schedulers
    
Ejemplo:
```ts
// src/domain/ports/in/CreateUserPort.ts
export interface CreateUserPort {
  execute(input: CreateUserDto): Promise<void>;
}
```

**Ports out (de salida)**

También llamados **puertos de salida** o _driven ports_.

**Qué hacen?**
Definen las **dependencias que el dominio necesita** para funcionar.

**Quién los implementa?**

Los **adaptadores de salida** como:
-  Repositorios que acceden a bases de datos
-  Clientes HTTP a servicios externos
-  Servicios de email, notificaciones, colas, etc.

Quién los llama?
Los **casos de uso**, desde el centro del dominio.

Ejemplo
```ts
// src/domain/ports/out/UserRepositoryPort.ts
export interface UserRepositoryPort {
  save(user: User): Promise<void>;
  findByEmail(email: string): Promise<User | null>;
}
```