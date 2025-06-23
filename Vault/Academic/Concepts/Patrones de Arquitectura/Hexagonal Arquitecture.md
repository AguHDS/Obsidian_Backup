---
tags:
  - Arquitecture_Pattern
  - backend
  - domain
  - adapters
  - entities
  - controller
  - middleware
  - useCases
---
---

**Antes de entender Hexagonal, hay que saber los [[Conceptos fundamentales]]**

---
### 🧠 Qué es la Arquitectura Hexagonal?

Es una arquitectura muy completa muy usada en el [[backend]]. El objetivo de la Arquitectura Hexagonal es **aislar el núcleo de la aplicación (lógica de negocio)** de los detalles externos como bases de datos, servicios web, interfaces gráficas, frameworks, etc.

 También conocida como:
- **Ports and Adapters**
- **Arquitectura limpia (parcialmente, pero no idéntica)**
- Parte del movimiento hacia arquitecturas más _desacopladas y mantenibles_.


---
### 🧭 ¿Cuándo usarla?

✅ **Casos ideales:**

- Aplicaciones complejas o críticas (ej. sistemas bancarios, ERPs)
- Necesidad de mantener la lógica de negocio desacoplada
- Requiere múltiples interfaces (REST, gRPC, CLI)
- Necesidad de testear la lógica sin depender de infraestructura
    
❌ **Evítala si:**
- El proyecto es muy pequeño o temporal
- La complejidad del dominio no lo justifica

---
### 🧪 Con qué tecnologias se usa?

 🔙 **Backend (donde más se usa)**

| Tecnología / Lenguaje       | Uso típico con Hexagonal                                                                           |
| --------------------------- | -------------------------------------------------------------------------------------------------- |
| **Node.js** + TS            | Muy común con Express, NestJS (que incluso ya usa DDD y patrones similares)                        |
| **Java (Spring Boot)**      | Súper común. Spring Boot + arquitectura hexagonal es una combinación muy usada en empresas grandes |
| **Python (FastAPI, Flask)** | Cada vez más frecuente, especialmente en proyectos con DDD y pruebas unitarias fuertes             |
| **C# (.NET Core)**          | Muy popular en arquitectura limpia; se adapta bien a hexagonal                                     |
| **Go (Golang)**             | Usado con interfaces y puertos claros, muy alineado con hexagonal                                  |
| **Kotlin**                  | En conjunto con Spring o Ktor, tiene buen soporte para hexagonal                                   |

🔧 Tecnologías que encajan con Hexagonal

|Capa|Tecnologías típicas|
|---|---|
|Dominio|Java/Kotlin/Go/Python/TS (puro, sin librerías)|
|Entrada (API)|FastAPI, Flask, Express.js, Spring Boot|
|Salida (DB)|SQLAlchemy, TypeORM, Spring Data, Repositories personalizados|
|Infraestructura|Docker, Kubernetes, Terraform, CI/CD|
|Testing|PyTest, JUnit, Jest|

> **Importante:** los frameworks deben estar en los adaptadores, no en el dominio.
> 
---
### 🧱 Comparación con otras arquitecturas

| Arquitectura       | Enfoque principal                  | Acoplamiento | Testabilidad | Escalabilidad |
| ------------------ | ---------------------------------- | ------------ | ------------ | ------------- |
| Monolítica         | Todo junto                         | Alto         | Media        | Baja          |
| MVC                | Separación UI-Lógica-DB            | Medio        | Media        | Media         |
| Hexagonal          | Dominio puro, adaptadores externos | Bajo         | Alta         | Alta          |
| Clean Arquitecture | Similar, más estricta y en capas   | Muy bajo     | Muy alta     | Alta          |


---

### 🔁 Similitud con Clean Arquitecture

Hexagonal arquitecture es parecida a [[Clean Arquitecture]] y hasta pueden usarse en conjunto en algunos casos.
Tienen [[Conceptos fundamentales]] compartidos como **Domain(core), Entities, Interfaces(ports), Adapters, entre otros**

Recapitulando estos conceptos:

 1. **Core (Dominio o Centro del hexágono)**
	Contiene la lógica de negocio pura.
	-  Entidades
	-  Casos de uso (application services)
	-  Interfaces (puertos) para dependencias externas
    
2. **Ports (Puertos)**
	Interfaces definidas por el dominio que describen qué necesita o puede ofrecer.
	-  Entrada: lo que la aplicación puede hacer (por ejemplo, `CreateUser`)
	-  Salida: lo que la aplicación necesita de afuera (por ejemplo, `UserRepository`)
    
 3. **Adapters (Adaptadores)**
	Implementan los puertos conectando el dominio con el mundo exterior.
	- Entrada: HTTP API, CLI, eventos
	- Salida: bases de datos, colas, servicios externos


---
### 🧩 **Hexagonal vs Clean Architecture**

| Aspecto                       | Arquitectura Hexagonal                  | Clean Architecture                                                             |
| ----------------------------- | --------------------------------------- | ------------------------------------------------------------------------------ |
| **Objetivo principal**        | Aislar el dominio de la infraestructura | Separar completamente las capas según responsabilidades                        |
| **Capa central**              | Dominio + casos de uso (a veces juntos) | Dominio (entidades)                                                            |
| **Forma de acoplamiento**     | Puertos (interfaces) y adaptadores      | Capas concéntricas con reglas estrictas                                        |
| **Dirección de dependencias** | Hacia el núcleo                         | Siempre hacia el centro                                                        |
| **Organización visual**       | Hexágono simbólico, práctico            | Anillos concéntricos (Entidades → Casos de uso → Interfaces → Infraestructura) |
| **Formalidad / Rigor**        | Más práctica y flexible                 | Más estructurada, con reglas de acceso entre capas                             |
| **Aplicación común**          | APIs, sistemas de dominio claro         | Software empresarial, mantenible a largo plazo                                 |
| **Popularidad**               | Alta en proyectos modernos              | Muy alta en proyectos grandes y en comunidades como Java, C#                   |
|                               |                                         |                                                                                |

**Se usan en conjunto Hexagonal y Clean?**

Sí, pueden **complementarse**. Muchos equipos
-  **Comienzan con arquitectura hexagonal**, por su **simplicidad y claridad**
-  Luego evolucionan hacia una **Clean Architecture** si el sistema crece o requiere una **modularidad más estricta**

✅ Entonces, cual elegir?
-  Si **quieres empezar ya**, la **Hexagonal** es ideal
-  Si el sistema es **muy grande y debe ser mantenible por años**, considera **Clean Architecture** desde el principio
-  **Ambas** están alineadas con los principios de **arquitectura dirigida por el dominio (DDD)**
  
  Piensa asi:
```ts
¿El dominio es complejo?
├── No
│   └── Usa MVC clásico o Monolito limpio
├── Sí
│   ├── ¿El equipo tiene experiencia con patrones?
│   │   ├── No
│   │   │   └── Usa Arquitectura Hexagonal
│   │   └── Sí
│   │       ├── ¿Hay necesidad de múltiples interfaces (REST, CLI, eventos)?
│   │       │   └── Usa Arquitectura Hexagonal
│   │       └── ¿Requieres modularidad, pruebas extremas y larga vida del software?
│   │           └── Usa Clean Architecture
```


---
### 📁 Estructura de Carpetas (TS + Node)

```ts
📦 src/
├── 📁 domain/                 ← Lógica del dominio (núcleo puro)
│   ├── 📁 entities/           ← Entidades del dominio
│   ├── 📁 value-objects/      ← Objetos de valor si aplican
│   ├── 📁 services/           ← Lógica de negocio (casos de uso)
│   └── 📁 ports/              ← Interfaces (puertos de entrada/salida)
│       ├── in/               ← Puertos de entrada (ej. CreateUserPort)
│       └── out/              ← Puertos de salida (ej. UserRepositoryPort)

├── 📁 application/            ← Casos de uso, orquesta dominio + puertos
│   └── 📁 use-cases/
│       ├── create-user/
│       │   ├── CreateUserService.ts
│       │   └── index.ts       ← Inyección de dependencias
│       └── ...

├── 📁 infrastructure/         ← Adaptadores concretos (DB, HTTP, etc.)
│   ├── 📁 database/
│   │   ├── 📁 prisma/
│   │   │   ├── client.ts
│   │   │   └── userRepository.adapter.ts ← Implementa puerto
│   │   └── prisma.schema
│   ├── 📁 http/
│   │   ├── 📁 controllers/
│   │   │   └── createUser.controller.ts  ← Implementa puerto de entrada
│   │   ├── routes.ts
│   │   └── server.ts
│   └── 📁 config/             ← Configuración general (dotenv, etc.)

├── 📁 shared/                 ← Utilidades comunes
│   ├── logger.ts
│   └── error/
│       ├── BaseError.ts
│       └── errorHandler.ts

├── 📁 tests/                  ← Tests unitarios e integración
│   ├── unit/
│   └── integration/

├── main.ts                   ← Entrada principal (start del server)
└── di.ts                     ← Configuración de inyección de dependencias
```