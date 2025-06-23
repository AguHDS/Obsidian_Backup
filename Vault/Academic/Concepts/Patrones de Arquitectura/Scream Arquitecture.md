---
tags:
  - backend
  - Arquitecture_Pattern
---
## 🗣️ ¿Qué es Screaming Architecture?

> Es una forma de organizar tu proyecto donde la estructura y nombres de carpetas "gritan" qué hace tu aplicación, no qué tecnologías usa

En lugar de organizar así (en función de frameworks o capas técnicas):
```
/controllers
/services
/models
/repositories
```
Lo organizas así (en función del **dominio o negocio**):
```
/payments
/orders
/inventory
```

---

## 🧱  Donde se usa mas?

### 🟩 **Backend (uso principal)**

-  Enfocado a aplicaciones orientadas a dominio (DDD, arquitectura limpia, hexagonal)
-  Muy útil para aplicaciones con **múltiples módulos de negocio**
-  Refuerza el enfoque en **funcionalidad** y no en herramientas

### 🟦 **Frontend (posible, pero menos común)**

-  Puede aplicarse en **React, Angular o Vue** cuando el frontend tiene lógica compleja o módulos de negocio claros.

Ejemplo:
```
/product
  ├── ProductView.tsx
  ├── productReducer.ts
  ├── useProduct.ts
/order
  ├── OrderForm.tsx
  ├── orderService.ts
```

