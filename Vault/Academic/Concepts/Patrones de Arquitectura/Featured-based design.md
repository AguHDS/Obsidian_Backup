---
tags:
  - Arquitecture_Pattern
  - frontend
  - react
  - vue
---

---
### 📌 ¿Qué es Feature-Based Design?

Es una forma de arquitectura para [[frontend]] donde la organizacion de las cosas se separa por caracteristicas (features), en lugar de tipo de archivo (components, services, pages, etc...)
Basicamente se crea una carpeta /features dentro del root (src), y dentro se crean carpetas que contiene cada feature (Ej: auth, dashboard, home, profile, etc...). Cada feature va a tener todo lo necesario para funcionar, sus pages, sus helpers, hooks, services, etc...
Luego, al mismo nivel de /features se crean carpetas para los recursos compartidos globales, como custom hooks, services, styles, redux, etc..

🔁 **Contrasta con la arquitectura tradicional en capas**, donde los archivos se agrupan por tipo (todos los componentes juntos, todos los servicios juntos, etc.).

Es especialmente útil en [[React]], ya que:

-  React **no impone estructura por defecto**
-  Tienes **libertad total** para organizar carpetas y archivos.
-  En proyectos grandes, el caos puede escalar rápido sin una buena arquitectura.
-  Por eso, Feature-Based Design se vuelve una solución **natural y popular en React**, especialmente con Redux, React Router, y herramientas modernas como Vite o Next.js.

Tambien se puede usar en [[Vue]]  o [[Angular]], pero Angular ya la integra como Modular Architecture,
en cambio Vue, si puede adoptarla tranquilamente. Incluso se pueden hacer combinaciones entre featured-based y Domain-ased o Layared-based.

---
### 🧭 ¿Cuándo usarla?

✅ **Casos ideales:**
- Proyectos medianos a grandes.
- Equipos trabajando en paralelo en múltiples funcionalidades.
- Aplicaciones modulares o que escalen en el tiempo.

❌ **No tan ideal si:**
- El proyecto es extremadamente pequeño o simple (puede ser overkill)

---

###  🔄Comparación con otros enfoques

| Enfoque              | Organización                          | Pros                          | Contras                                         |
| -------------------- | ------------------------------------- | ----------------------------- | ----------------------------------------------- |
| Layer-based          | Por tipo (components, services, etc.) | Simple, intuitivo             | Dificultad de escalar, alta dependencia cruzada |
| Feature-based        | Por funcionalidad                     | Modular, aislado, escalable   | Overhead inicial, curva de aprendizaje          |
| Domain-Driven Design | Por dominio del negocio               | Ideal para sistemas complejos | Mayor inversión en modelado inicial             |
|                      |                                       |                               |                                                 |

---
### 📁 Ejemplo de estructura

```ts
src/
│
├── app/                          # Configuración global de la aplicación
│   ├── store.ts                  # Configuración del store de Redux
│   ├── hooks.ts               # Hooks personalizados globales (ej: useAppDispatch)
│   └── routes.tsx     # Definición de rutas principales (enrutamiento de features)
│
├── features/                     # Cada carpeta representa una feature
│   ├── auth/
│   │   ├── components/           # Componentes UI propios de auth
│   │   ├── services/             # Lógica de acceso a API, axios o fetch
│   │   ├── slices/               # Redux slice + estado inicial, reducers, etc.
│   │   ├── hooks/                # Hooks relacionados (useLogin, useAuth)
│   │   ├── types/                # Tipos o interfaces de auth
│   │   ├── pages/                # Vistas completas (LoginPage, RegisterPage)
│   │   └── index.ts              # barrel (para importar limpio)
│
│   ├── dashboard/
│   │   ├── components/
│   │   ├── services/
│   │   ├── slices/
│   │   ├── hooks/
│   │   ├── types/
│   │   ├── pages/
│   │   └── index.ts
│
│   ├── settings/
│   │   ├── components/
│   │   ├── services/
│   │   ├── pages/
│   │   └── index.ts
│
├── shared/                       # Código reutilizable entre todas las features
│   ├── components/               # Ej: Button, Input, Modal, Loader
│   ├── layout/                   # Layouts globales (MainLayout, AuthLayout)
│   ├── hooks/                    # useDebounce, useOutsideClick
│   ├── utils/                    # funciones utilitarias (formatDate, groupBy)
│   ├── constants/                # textos, enums, etc.
│   ├── services/                 # servicios globales: AuthService, ApiClient
│   └── types/                    # tipos globales de ts (User, ApiResponse)
│
├── assets/                       # Imágenes, fuentes, íconos
│   ├── icons/
│   └── images/
│
├── styles/                       # Estilos globales, Tailwind, variables
│   └── globals.css
│
├── index.tsx                     # Punto de entrada principal
└── main.tsx                # Inicialización de React App (usando Vite por ejemplo)

```

Entonces, la parte de auth contrnadria algo como esto:

```ts
features/auth/
│
├── components/
│   ├── LoginForm.tsx
│   ├── RegisterForm.tsx
│
├── pages/
│   ├── LoginPage.tsx
│   └── RegisterPage.tsx
│
├── services/
│   └── authService.ts           # Funciones login, register, etc.
│
├── slices/
│   └── authSlice.ts             # Redux slice de autenticación
│
├── hooks/
│   └── useAuth.ts               # Hook para acceder a usuario actual
│
├── types/
│   └── authTypes.ts             # Tipos de LoginPayload, User, etc.
│
└── index.ts                     # Exporta componentes, slice, hooks
```