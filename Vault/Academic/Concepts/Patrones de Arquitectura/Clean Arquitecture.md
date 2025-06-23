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

**Antes de entender clean arquitecture, hay que saber los [[Conceptos fundamentales]]**

---

### ❓ Que hace única a Clean Arquitecture?

Clean Arquitecture es principalmente usada en el [[backend]], aunque tambien puede ser usada en el [[frontend]] avanzado, aunque esto no es tan común
A diferencia de MVC, donde las dependencias a veces se vuelven circulares o van en todas direcciones (especialmente en implementaciones desordenadas), Clean Architecture impone una **regla de oro**:

> **Las dependencias siempre deben apuntar hacia el centro** (hacia el dominio)
> **El "centro"** de esta arquitectura es el **modelo de dominio**: entidades y reglas de negocio puras.

Las **capas externas** (como UI, base de datos, frameworks, API, etc.) pueden cambiar, pero las reglas del dominio deben permanecer estables.

Esto significa:
-  El dominio no sabe nada de la base de datos, ni de HTTP, ni del framework.
-  Pero la base de datos sí conoce el dominio (para guardar sus objetos).
-  Igual con los controladores: ellos orquestan casos de uso, pero no contienen sus reglas/lógica de negocio.

✅ **Comparación**:
-  **MVC**: El modelo a veces llama al controlador o a la vista si no se manejabien.
-  **Clean**: El dominio nunca depende de nada. Solo es llamado, nunca llama.
- **Hexagonal**: También sigue este principio, pero Clean lo hace aún más explícito con capas concéntricas.

#### 1. **Las reglas de negocio están completamente aisladas**

En Clean Architecture, el **núcleo del sistema (Entidades + Casos de uso)** se puede:
- **Testear sin mocks** de base de datos ni de frameworks
- Ejecutar sin siquiera tener conectada una API real
    
Esto va más allá de simplemente tener un "Modelo" en MVC. Clean Architecture diferencia entre:
- **Entidades (negocio puro)**
- **Casos de uso (lógica de aplicación)**
- **Interfaces externas (adaptadores)**
    
✅ **Comparación**:
- **MVC** mezcla lógicas de negocio en los modelos o incluso en los controladores.
- **Onion** es parecida, pero Clean le da **más flexibilidad a los adaptadores**, permitiendo múltiples controladores, interfaces web, móviles, etc., hablando con los mismos casos de uso.


#### 2. **La flexibilidad con los adaptadores**

En Clean Architecture, puedes tener
-  Varios **adaptadores/drivers de entrada**: API REST, WebSocket, CLI, tareas cron, etc.
-  Varios **adaptadores/drivers de salida**: PostgreSQL, MongoDB, S3, colas de mensajes, etc.

Todos adaptadores que **conectan** al núcleo mediante interfaces. Esto permite cambiar **implementaciones** sin tocar el core, y ayuda a un concepto que cambia con el tiempo.

✅ **Comparación**:
-  **MVC tradicional** no distingue entre interfaces de entrada/salida. Solo piensa en HTTP.
-  **Hexagonal Architecture** (o Ports and Adapters) ya hacía esto, pero Clean lo lleva a un nivel más "completo", unificando mejor las capas.

#### 3. **Estructura concéntrica visual y explícita**

Mientras que Hexagonal o Onion son parecidas, Clean Architecture se distingue por
-  **Visualizar las capas como círculos** (modelo concéntrico)
-  Dar nombres específicos: _Entidades_, _Casos de uso_, _Adaptadores_, _Frameworks_
    
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
    -  Express y todo lo relacionado a la entrega HTTP, va en interfaces
    -  Ej: `UserController`, RserRepositoryImpl
        
4. **Infrastructure / Frameworks (UI, DB, Web)**
    -  Frameworks, tecnologías concretas
    -  Ej: APIs externas, Mongo, postgre, servicios de email, etc

**Si algo entra a tu sistema (input), va en `interfaces/`.**  (procesos http, routes, middlewares, controllers)
**Si tu sistema accede a algo externo (output), va en `infrastructure/`.** (conectar a una db, por ejemplo, o usar la tecnologia externa para jwt)

---
### 📁 Estructura de carpetas (Backend node)
Muy usado en Backends robustos

```
/src
│
├── domain/                     # Entidades y lógica de negocio pura
│   ├── entities/
│   │   └── User.ts
		└── UserProfile.ts
│   └── repositories/
│       └── IUserRepository.ts  # Interface (sin implementación)
	│└── ports/
	│       └── FileUploader.ts # interface para cloudinary
├── application/                # Casos de uso / servicios de aplicación
│   ├── useCases/
│   │   └── RegisterUser.ts
│   └── services/              # Coordinación entre entidades y useCases
                              No debe depender de frameworks ni librerías externas
│       └── HashPassword.ts
│
├── infrastructure/             # Toda la implementación de tecnologías externas
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
│   │   └── jwt.ts            # handleJwt.ts # usa JwtUserData
		└── handlePassword.ts # Usa bcrypt
	└── adapters/CloudinaryUploader.ts # adaptador que usa el port FileUploader.ts
│   └── config/
│       └── env.ts
│
├── interfaces/   # Adaptadores para el mundo exterior (web/API): Express(Request,                      Response, Next), routers, middlewares
│   ├── http/
│   │   ├── middlewares/
│   │   │   └── authMiddleware.ts
			└── decodedRefreshToken.ts # Porque trabaja con Request (express)
│   │   └── server.ts
│
├── shared/                     # Utilidades compartidas
│   ├── types/
│   └── utils/
│       └── logger.ts
│
└── index.ts                    # Punto de entrada (arranca el servidor)

```

---

### 📁 Estructura de carpetas (Frotend React)
No es lo mas común en frontend, pero se puede usar en proyectos grandes. La mas usada para react es **Featured-based design**. Tambien se puede mezclar Featured-based con Clean arquitecture para React.
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


---

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
  
