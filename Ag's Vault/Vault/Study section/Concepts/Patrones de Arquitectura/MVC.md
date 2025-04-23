---
tags:
  - Arquitecture_Pattern
  - backend
  - middleware
  - controller
  - routes
  - Business_Logic
  - models
---
---

### ❗ Conceptos fundamentales

Primero, hay varios conceptos que hay que entender:
-  Que es Logica de negocio? -> [[Business Logic]]
-  Que es "View" en MVC en el backend?
-  [[Controllers]], [[Routes]], [[Middlewares]]

#### Lógica de negocio

. Resumidamente, logica de negocio es la lógica(código) que maneja validaciones, guardar en base de datos, interacciones con APIS y cualquier lógica completa. Esto va en **Models**

#### View(vista)

. Si usamos MVC en el backend podemos confundir vista con el frontend, pero no lo es.
  Es basicamente, la respuesta que devolvemos al cliente, y puede tener datos que el cliente va a usar para renderizar en el frontend, por ejemplo:
```ts
{
  id: 123,
  name: "Alejandro",
  email: "alejandro@example.com",
  password: "hash...",
  createdAt: "2025-04-22T10:00Z"
}
```
Esos datos en MVC serian la vista, porque es lo que el cliente va a usar.

#### Controllers

. El controller es el que recibe la petición y llama a los **Models**, para ejecutar su logica de negocio, y así responder al cliente. (se asume que ya sabes lo que son los [[Middlewares]] y [[Routes]])
	Flujo típico:
     - El cliente hace una petición a la ruta/endpoint "api/login"
     - La ruta recibe la petición, y pasa el siguiente paso al **middleware**, que hace validaciones
     - Si todo esta ok, el middleware pasa la tarea al controller, que ejecuta la logica de negocio traída desde los Models para enviar respuesta al cliente.

---

### 🏷️ ¿Qué es MVC?

> **MVC (Model-View-Controller)** es un patrón de arquitectura muy usado en el backend (aunque también puede ser usado en el frontend), que **separa una aplicación en tres componentes principales**:

- **Model (Modelo)**: maneja los datos y la lógica de negocio
- **View (Vista)**: representa la interfaz de usuario
- **Controller (Controlador)**: Maneja la logica de negocio importandola de los models para enviar una respuesta al cliente. El controller no debe tener logica de negocio, mientras mas limpio esté, mejor.

Entonces,
#### 📦 **1. Model (Modelo)**

- Representa los **datos** y **la lógica de negocio**.
- No sabe nada de la vista o del controlador.
- Suele contener:
    - Reglas de negocio
    - Lógica de persistencia (base de datos, archivos)
    - Validaciones

#### 🎨 **2. View (Vista)**

- Es la **interfaz de usuario**, o partes, de ella que enviamos en respuesta
- Presenta los datos del modelo al usuario.
- Suele ser una plantilla (HTML, UI de app, etc.)
- Nunca modifica directamente el modelo.

#### 🧭 **3. Controller (Controlador)**

- Actúa como **puente entre vista y modelo**.
- Escucha eventos del usuario (clics, formularios).
- Actualiza el modelo o selecciona qué vista mostrar.


---
### 🧠 Usar paradigma POO o funcional?
MVC brilla más cuando usamos POO, incluso en proyectos con TS+Node. Aunque tambien se usa con programación funcional en proyectos mas simples con Javascript

---
### 🧪 Ejemplo real de uso usando POO

1. Archivo principal (app.ts, server.ts, index.ts)
```ts
// src/index.ts
import express from "express";
import userRoutes from "./routes/userRoutes";

const app = express();
app.use(express.json());

app.use("/api/users", userRoutes);

app.listen(3000, () => {
  console.log("Servidor escuchando en http://localhost:3000");
});
```

2. `userRoutes.ts` – Rutas del módulo usuario
```ts
// src/routes/userRoutes.ts
import { Router } from "express";
import { UserController } from "../controllers/UserController";
import { UserService } from "../services/UserService";
import { authMiddleware } from "../middleware/authMiddleware";

const router = Router();
const userService = new UserService();
const userController = new UserController(userService);

router.get("/profile", authMiddleware, userController.getUserProfile);

export default router;
```

3. `UserModel.ts` – Modelo con datos simulados
 ```ts
 // src/models/UserModel.ts
export interface User {
  id: string;
  name: string;
  role: string;
}

export class UserModel {
  private static users: User[] = [
    { id: "1", name: "Alice", role: "admin" },
    { id: "2", name: "Bob", role: "user" },
  ];

  static findById(id: string): User | null {
    return this.users.find(user => user.id === id) || null;
  }
}
```

4. `UserService.ts` – Lógica de negocio
```ts
// src/services/UserService.ts
import { UserModel, User } from "../models/UserModel";

export class UserService {
  getUserById(id: string): User | null {
    return UserModel.findById(id);
  }
}
```

5. `authMiddleware.ts` – Middleware de autenticación, next() delega al Controller
```ts
// src/middleware/authMiddleware.ts
import { Request, Response, NextFunction } from "express";

export function authMiddleware(req: Request, res: Response, next: NextFunction) {
  // Simulación: extraemos user ID desde un token ficticio
  const authHeader = req.headers.authorization;

  if (!authHeader || !authHeader.startsWith("Bearer ")) {
    return res.status(401).json({ error: "No autorizado" });
  }

  const token = authHeader.split(" ")[1];

  // Supongamos que el token es simplemente el ID del usuario
  req.user = { id: token }; // Esto se puede tipar mejor con interfaces

  next();
}
```

6. `UserController.ts` – El controller se ejecuta luego del middleware, y envia respuesta
```ts
// src/controllers/UserController.ts
import { Request, Response } from "express";
import { UserService } from "../services/UserService";

export class UserController {
  constructor(private userService: UserService) {}

  getUserProfile = (req: Request, res: Response): void => {
    const userId = req.user?.id; // `user` viene del middleware

    const user = this.userService.getUserById(userId);

    if (!user) {
      res.status(404).json({ error: "Usuario no encontrado" });
    } else {
      res.json({ id: user.id, name: user.name, role: user.role });
    }
  };
}
```


---
### 📁 Ejemplo de estructura de carpetas
/src
│
├── /controllers          # Controladores: coordinan flujo entre request, service y response
│   └── UserController.ts
│
├── /models               # Modelos de datos (entidades, ORM, lógica de DB)
│   └── UserModel.ts
│
├── /services             # Lógica de negocio pura (puede usar modelos)
│   └── UserService.ts
│
├── /routes               # Rutas: conectan endpoints con controladores
│   └── userRoutes.ts
│
├── /middleware           # Middlewares: auth, logging, validaciones, etc.
│   └── authMiddleware.ts
│
├── /utils                # Funciones reutilizables, helpers, validadores
│   └── formatResponse.ts
│
├── /config               # Configuración general: DB, env, logger
│   └── db.ts
│   └── env.ts
│
├── /interfaces           # Tipos e interfaces globales (TypeScript)
│   └── User.ts
│   └── Express.d.ts      # Para extender req.user
│
├── index.ts              # Punto de entrada de la app
└── app.ts                # Configura app y middlewares, exporta instancia


---
### 🔁 Cuando usar?

| Tamaño del proyecto | ¿Usar MVC?              | Comentario                                                   |
| ------------------- | ----------------------- | ------------------------------------------------------------ |
| Pequeño             | Opcional / Simplificado | Útil si planeas escalar o estás practicando arquitectura     |
| Mediano             | ✅ Recomendado           | Aca es donde MVC brilla                                      |
| Grande              | 🔹 Con adaptaciones     | Integrarlo en una arquitectura mayor (Clean, Hexagonal, etc) |
