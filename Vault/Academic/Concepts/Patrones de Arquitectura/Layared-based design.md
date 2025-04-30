---
tags:
  - Arquitecture_Pattern
  - frontend
  - react
  - angular
---
---

**Antes de entender Layared Arquitecture, se asume que ya sabes los [[Conceptos fundamentales]]**

---
### 📌 ¿Qué es?

Layered Architecture es un **modelo clásico de diseño de software** que divide una aplicación en **capas bien definidas**, donde cada capa tiene una **responsabilidad específica** y solo puede comunicarse con la capa inmediatamente inferior o superior.

Es una forma de aplicar **separación de responsabilidades (SoC)** en la arquitectura de una aplicación. Recomendable para proyectos con [[React]] o [[Angular]]

---
### 🧭 ¿Cuándo usarla?

✅ Casos ideales:
-  Aplicaciones CRUD medianas a grandes.
-  Equipos con roles especializados (frontend, backend, BDD).
-  Proyectos donde necesitas escalar lógica de negocio o infraestructura.
    

❌ No tan ideal si:
- El proyecto es extremadamente simple.
- Se busca alta modularidad por funcionalidades → usar feature-based o modular monolith.

---
### 📊 ¿Para qué tamaño de proyecto es ideal?

| Tamaño del Proyecto | ¿Layered es recomendable?                                        | Notas                                                                            |
| ------------------- | ---------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| 🟢 Pequeño          | ✅ Si se simplifica                                               | Útil para mantener orden sin sobrecargar                                         |
| 🟡 Mediano          | ✅ Ideal                                                          | Excelente equilibrio entre orden y esfuerzo                                      |
| 🔴 Grande           | ⚠️ Solo si se combina con modularidad (features o domain-driven) | Puede volverse rígida o difícil de escalar sin subdividir por módulos o dominios |


---
### 📊 Con que tecnología se usa mas?

| Framework / Tecnología | ¿Se usa arquitectura en capas? | Comentario                                                                                                                                                                              |
| ---------------------- | ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **React**              | ✅ Muy común                    | Se aplica usando carpetas como `presentation/`, `domain/`, `infrastructure/`. Permite desacoplar lógica y facilita pruebas unitarias.                                                   |
| **Angular**            | ✅ Súper natural                | Angular **empuja por defecto** hacia una arquitectura en capas (con módulos, servicios, componentes separados). Muy usada en empresas grandes.                                          |
| **Vue.js**             | ⚠️ Menos común                 | Vue se usa mucho con enfoque **feature-based** o con archivos `.vue` que contienen todo (template, script, style). Pero puede adaptarse a capas si se organiza manualmente.             |
| **Vanilla JS**         | ❌ Rara vez se usa              | Difícil de mantener arquitectura en capas sin un framework. No hay separación automática de responsabilidades ni componentes. Se tiende a usar módulos simples o estructura por páginas |

---

### 🧱 Estructura típica realista para frontend (React + TypeScript + Axios + Tailwind)

```ts
src/
│
├── presentation/                  # Componentes UI y páginas
│   ├── components/                # UI atómica: Button, Modal, Card
│   ├── pages/                     # Pantallas: HomePage, ProfilePage
│   └── layout/                    # Layouts generales (con Navbar, Sidebar)
│
├── application/                   # Casos de uso, lógica de flujo
│   ├── usecases/                  # loginUser, fetchUserProfile, updateSettings
│   ├── services/             # Cosas que llaman a infrastructure y domain
│   └── validators/                # Validaciones específicas de flujo
│
├── domain/                        # Modelos y lógica empresarial pura
│   ├── models/                    # User, Session, Product, etc.
│   ├── entities/                  # Clases o estructuras de dominio
│   └── interfaces/                # Contratos: AuthService, ProductRepository
│
├── infrastructure/               # Implementaciones técnicas reales
│   ├── api/                       # Axios client, endpoints, interceptors
│   ├── repositories/             # Implementación de interfaces de domain
│   ├── storage/                  # LocalStorage, cookies, etc.
│   └── adapters/             # Adaptadores entre estructuras externas y dominio
│
├── shared/                       # Código utilitario global
│   ├── utils/                    # formatDate, debounce, etc.
│   ├── hooks/                    # useWindowSize, useOutsideClick
│   ├── constants/                # enums, valores estáticos
│   └── types/                    # tipos comunes globales
│
├── styles/                       # Estilos globales (Tailwind + resets)
│   └── index.css
│
├── app.tsx                       # Configuración base de la app
└── main.tsx                      # Entry point (ReactDOM render + providers)

```