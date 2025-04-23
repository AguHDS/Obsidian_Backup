---
tags:
  - controller
  - middleware
  - entities
  - adapters
  - useCases
  - domain
  - backend
---
---
### ❗ Conceptos fundamentales

Cosas que hay que saber: **entities, repositories, domain, useCases, infraestructure, Adapters**

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
### ❓ Que hace única a Clean Arquitecture?

A diferencia de MVC, donde las dependencias a veces se vuelven circulares o van en todas direcciones (especialmente en implementaciones desordenadas), Clean Architecture impone una **regla de oro**:
> **Las dependencias siempre deben apuntar hacia el centro** (hacia el dominio)

Esto significa:
- El dominio no sabe nada de la base de datos, ni de HTTP, ni del framework.
- Pero la base de datos sí conoce el dominio (para guardar sus objetos).
- Igual con los controladores: ellos orquestan casos de uso, pero no contienen sus reglas/lógica de negocio.

✅ **Comparación**:
- **MVC**: El modelo a veces llama al controlador o a la vista si no se manejabien.
- **Clean**: El dominio nunca depende de nada. Solo es llamado, nunca llama.
- **Hexagonal**: También sigue este principio, pero Clean lo hace aún más explícito con capas concéntricas.

#### 1. **Las reglas de negocio están completamente aisladas**

En Clean Architecture, el **núcleo del sistema (Entidades + Casos de uso)** se puede:
- **Testear sin mocks** de base de datos ni de frameworks.
- Ejecutar sin siquiera tener conectada una API real.
    
Esto va más allá de simplemente tener un "Modelo" en MVC. Clean Architecture diferencia entre:
- **Entidades (negocio puro)**
- **Casos de uso (lógica de aplicación)**
- **Interfaces externas (adaptadores)**
    
✅ **Comparación**:
- **MVC** mezcla lógicas de negocio en los modelos o incluso en los controladores.
- **Onion** es parecida, pero Clean le da **más flexibilidad a los adaptadores**, permitiendo múltiples controladores, interfaces web, móviles, etc., hablando con los mismos casos de uso.


#### 2. **La flexibilidad con los adaptadores**

En Clean Architecture, puedes tener
- Varios **drivers de entrada**: API REST, WebSocket, CLI, tareas cron, etc.
- Varios **drivers de salida**: PostgreSQL, MongoDB, S3, colas de mensajes, etc.
    
Todos adaptadores que **conectan** al núcleo mediante interfaces. Esto permite cambiar **implementaciones** sin tocar el core, lo cual es clave en sistemas que cambian con el tiempo.

✅ **Comparación**:
- **MVC tradicional** no distingue entre interfaces de entrada/salida. Solo piensa en HTTP.
- **Hexagonal Architecture** (o Ports and Adapters) ya hacía esto, pero Clean lo lleva a un nivel más "completo", unificando mejor las capas.

#### 3. **Estructura concéntrica visual y explícita**

Mientras que Hexagonal o Onion son parecidas, Clean Architecture se distingue por
- **Visualizar las capas como círculos** (modelo concéntrico)
- Dar nombres específicos: _Entidades_, _Casos de uso_, _Adaptadores_, _Frameworks_
    
Este modelo ayuda a **pensar el diseño desde adentro hacia afuera**, algo que no es tan intuitivo en otras arquitecturas.

---
### 🧪 Capas de Clean Arquitecture

      +-------------------------+
      |      External Layer     |   → Frameworks (React, Express, Flask, DB)
      +-------------------------+
      |    Interface Adapters    |   → Controllers, Presenters, Gateways
      +-------------------------+
      |      Application Core    |   → Casos de uso (Application/Service Layer)
      +-------------------------+
      |       Domain Layer       |   → Entidades (Negocio puro)
      +-------------------------+

 **Descripción de las capas**:
 
1. **Domain Layer (Entidades)**
    -  Contiene las **reglas de negocio más generales**
    -  No depende de nada externo
    -  Ej: `User`, `Order`, `Product`, etc.
        
2. **Application Layer (useCases / Services)**
    -  Lógica de aplicación que **usa las entidades**
    -  Orquesta las operaciones del dominio
    -  Ej: `CreateUser`, `ProcessPayment`, etc.
        
3. **Interface Adapters (Controllers / Presenters / Gateways)**
    -  Adaptadores que permiten comunicar el mundo externo con la lógica interna
    -  Ej: `UserController`, `UserRepositoryImpl`
        
4. **Infrastructure / Frameworks (UI, DB, Web)**
    -  Frameworks, tecnologías concretas
    -  Ej: React, Express, MongoDB, PostgreSQL

---
### 📁 Estructura de carpetas (Frotend React)
No es la mas común para react, pero se puede usar en proyectos grandes. La mas usada para react es **Featured-based design**. Tambien se puede mezclar Featured-based con Clean arquitecture para React.
Si querés adaptar Clean Arquitecture sin React, modifica esta estructura para que no use react y se adapte a vanilla js/ts, angular, o necesidades de tu proyecto

```
/src
│
├── domain/                      # Modelo puro de negocio, sin dependencias
│   ├── models/         # Interfaces o clases de entidades (User, products, etc...)
│   │   └── User.ts
│   ├── valueObjects/          # Objetos de valor (Email, Password)
│   └── services/              # Lógica de dominio pura (ej. validadores)
│
├── application/                # Casos de uso de frontend (interacción usuario)
│   ├── useCases/
│   │   └── registerUser.ts
│   └── interfaces/            # Interfaces de abstracción (ej. UserRepository)
│       └── IUserRepository.ts
│
├── infrastructure/        # Implementaciones concretas (fetch, localstorage,etc)
│   ├── api/
│   │   └── userApi.ts         # Conexiones HTTP
│   └── repositories/
│       └── UserRepository.ts  # Implementación concreta de IUserRepository
│
├── presentation/               # UI pura (sin lógica de dominio)
│   ├── components/            # Botones, inputs, formularios
│   │   └── UserForm.tsx
│   ├── pages/                 # Vistas completas (una por ruta)
│   │   └── RegisterPage.tsx
│   └── hooks/                 # Custom hooks (useRegisterUser, useUser)
│
├── shared/                     # Código reutilizable (tipos, helpers, estilos)
│   ├── utils/
│   └── constants/
│
└── main.tsx                    # Entry point (render + router + provider)
```

### 📁 Estructura de carpetas (Backend node)
Muy usado en Backends robustos

```
/src
│
├── domain/                     # Entidades y lógica de negocio pura
│   ├── entities/
│   │   └── User.ts
│   ├── valueObjects/
│   │   └── Email.ts
│   └── repositories/
│       └── IUserRepository.ts # Interface (sin implementación)
│
├── application/                # Casos de uso / servicios de aplicación
│   ├── useCases/
│   │   └── RegisterUser.ts
│   └── services/              # Coordinación entre entidades y lógica
│       └── HashPassword.ts
│
├── infrastructure/             # Todo lo que depende de tecnología externa
│   ├── database/
│   │   ├── prisma/            # ORM específico
│   │   │   └── PrismaClient.ts
│   │   └── UserRepository.ts  # Implementa IUserRepository
│   ├── http/
│   │   ├── routes/
│   │   │   └── userRoutes.ts
│   │   └── controllers/
│   │       └── UserController.ts
│   ├── auth/
│   │   └── jwt.ts
│   └── config/
│       └── env.ts
│
├── interfaces/              # Interfaces externas como controladores, middlewares
│   ├── http/
│   │   ├── middlewares/
│   │   │   └── authMiddleware.ts
│   │   └── server.ts
│
├── shared/                     # Utilidades compartidas
│   ├── types/
│   └── utils/
│       └── logger.ts
│
└── index.ts                    # Punto de entrada (arranca el servidor)

```

### ✔️ Cuando usar Clean Arquitecture?

Clean arquitecture es mas usada en **Backend**, pero tambien se puede aplicar en el front (para el front, personalmente prefiero featured-based design para proyectos medianos-grandes, y para proyectos muy grandes, featured-based + atomic design)

| Criterio / Necesidad                                             | MVC | Clean Architecture | Hexagonal Architecture |     |
| ---------------------------------------------------------------- | --- | ------------------ | ---------------------- | --- |
| Proyecto pequeño o prototipo rápido                              | ☑️  | ☐                  | ☐                      |     |
| Aplicación CRUD simple con una sola fuente de entrada (HTTP)     | ☑️  | ☐                  | ☐                      |     |
| Reglas de negocio sencillas                                      | ☑️  | ☐                  | ☐                      |     |
| Necesitas crecer el proyecto en el futuro                        | ☐   | ☑️                 | ☑️                     |     |
| Requiere alta testabilidad                                       | ☐   | ☑️                 | ☑️                     |     |
| Interacción con múltiples interfaces (API, CLI, cola de eventos) | ☐   | ☑️                 | ☑️                     |     |
| Separación clara entre negocio y tecnología                      | ☐   | ☑️                 | ☑️                     |     |
| Probabilidad de migrar base de datos o framework                 | ☐   | ☑️                 | ☑️                     |     |
| Necesitas aplicar DDD (Domain-Driven Design)                     | ☐   | ☑️                 | ☑️                     |     |
| Equipo grande / múltiples equipos trabajando en capas distintas  | ☐   | ☑️                 | ☑️                     |     |
| Prefieres reglas explícitas y estructura más rígida              | ☐   | ☑️                 | ☐                      |     |
| Prefieres flexibilidad con entradas y salidas (ports/adapters)   | ☐   | ☐                  | ☑️                     |     |
| Aplicación orientada a eventos o microservicios                  | ☐   | ☑️                 | ☑️                     |     |

- ✅ Usa **MVC** si: es un proyecto pequeño, rápido, con lógica sencilla, y solo una interfaz.
    
- ✅ Usa **Clean Architecture** si: necesitas escalar, testear bien, cambiar tecnología, o tienes lógica de negocio seria.
    
- ✅ Usa **Hexagonal** si: vas a interactuar con múltiples sistemas y necesitas máxima independencia de entrada/salida.