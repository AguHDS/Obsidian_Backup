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

Tambien se puede usar en [[Vue]]  o [[Angular (index)]], pero Angular ya la integra como Modular Architecture,
en cambio Vue, si puede adoptarla tranquilamente. Incluso se pueden hacer combinaciones entre featured-based y Domain-ased o Layared-based.

---
### Que es una feature?

Una **feature (funcionalidad)** representa una **unidad funcional autónoma** de la aplicación. Es decir:

> Un conjunto coherente de componentes, lógica, rutas, hooks, estado, etc. que juntos **cumplen un objetivo funcional concreto para el usuario**.

Requisitos para que algo califique como **feature**

| Requisito                                             | ¿Por qué es importante?                                 |
| ----------------------------------------------------- | ------------------------------------------------------- |
| ✅ Tiene una **responsabilidad funcional clara**       | Debe resolver algo concreto (ej: login, perfil, upload) |
| ✅ Puede tener **una o más páginas**                   | Ej: `/login`, `/profile`, `/dashboard`, etc.            |
| ✅ Tiene lógica propia (opcional)                      | Ej: API calls, hooks, validaciones, estado local/global |
| ✅ Tiene componentes **específicos y encapsulados**    | No deberían vivir en `shared` si son exclusivos         |
| ✅ Idealmente **puede evolucionar independientemente** | Ayuda al mantenimiento y escalabilidad del proyecto     |

---
### 🧭 ¿Cuándo usarla?

✅ **Casos ideales:**
-  Proyectos medianos a grandes.
-  Equipos trabajando en paralelo en múltiples funcionalidades.
-  Aplicaciones modulares o que escalen en el tiempo.

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
│   ├── /redux                  # Configuración del store de Redux
│   ├── /hooks        # Hooks que interactuan con el nucleo de la App (si aplica)
│   └── /routes     # Definición de rutas principales (enrutamiento de features)
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
│   ├── utils/                    # funciones utilitarias (formatDate, groupBy)
│   ├── constants/                # textos, enums, etc.
│   ├── services/                 # servicios globales: AuthService, ApiClient
│   └── hooks/                    # hooks globales reutilizables (ej: useFetch)
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

---

### 📦 Ubicación Smart & Dumb components

Los smarts & Dumb components deben ir en el lugar correcto

| Tipo de componente                        | ¿Dónde debería ir?                            | Ejemplos comunes                                      |
| ----------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| 🟠 **Dumb de uso general (reutilizable)** | `shared/components/`                          | `Button`, `Input`, `Card`, `Spinner`, `Tooltip`, etc. |
| 🟠 **Dumb de uso específico**             | `features/[feature]/components/`              | `AuthForm`, `ProfilePicture`, `UploadPreview`         |
| 🔵 **Smart de uso específico**            | `features/[feature]/components/`              | `LoginContainer`, `DashboardWidget`, `FileUploader`   |
| 🔵 **Smart de uso general (muy raro)**    | `shared/containers/` **o** `shared/ui/logic/` | `ProtectedRoute`, `PersistLogin`, `ErrorBoundary`     |
|                                           |                                               |                                                       |
