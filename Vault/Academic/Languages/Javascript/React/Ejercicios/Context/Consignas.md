---
tags:
  - react
  - practica
  - context
  - ejercicios
---


## **Ejercicios de práctica: React Context y optimización**

---
### **Ejercicio 1: Crear un Context sin ayuda de IA** ✅

**Objetivo:** Practicar la implementación manual de un contexto en React, entendiendo su estructura y cómo compartir datos entre componentes sin prop drilling.

**Consigna:**

1. Crea un contexto llamado `ThemeContext` que permita alternar entre modo claro y oscuro (`light | dark`).
2. Implementa el `ThemeProvider` que controle el estado del tema.
3. Crea componentes hijos que usen el contexto para:
    - Mostrar el tema actual.
    - Alternar entre temas con un botón.
4. No uses código generado por IA. La práctica debe ser 100% manual para consolidar la comprensión.

---

### **Ejercicio 2: Compartir datos de contexto a un componente que está fuera del Provider**

**Objetivo:** Resolver el caso donde un componente necesita acceder a datos derivados del contexto, pero no puede estar dentro del `Provider` porque no consume el contexto directamente.

**Consigna:**

1. Crea un contexto que exponga datos de usuario (`username`, `role`).
2. El componente `App` renderiza:
    
    - `UserProvider`, que contiene el layout principal.
        
    - Un componente `Header` que está fuera del `UserProvider`.
        
3. `Header` necesita mostrar el `username`, pero no puede estar dentro del `Provider`.
4. Optimiza la solución sin duplicar lógica. Usa lifting, props o una solución que mantenga el código limpio y eficiente.
    

---

### **Ejercicio 3: Dividir un contexto en múltiples contextos especializados** ✅

**Objetivo:** Aprender a evitar renders innecesarios dividiendo un `Context` monolítico en varios más específicos.

**Consigna:**

1. Crea una app con un `UserContext` que contenga:
    - `username`
        
    - `email`
        
    - `isAdmin`
        
2. Luego, divídelo en 2 contextos:
    
    - `UserInfoContext` (`username`, `email`)
        
    - `UserPermissionsContext` (`isAdmin`)
3. Usa `React.memo` y/o una solución como `use-context-selector` para asegurarte de que los componentes hijos solo se rendericen si la parte del contexto que consumen cambia.

#### ¿Por qué hacer esto?

Cuando usas un solo `Context` con muchos valores (por ejemplo: `username`, `email`, `isAdmin`, `token`, `theme`, etc.), **cualquier cambio en uno de esos valores causa un re-render en todos los componentes que consumen el contexto completo**, aunque solo usen una parte.

Separar el contexto en varios más pequeños permite que **los componentes solo se re-rendericen si cambia el valor específico que usan**.

---

### **Ejercicio 4: Context con lógica asincrónica + manejo de estado complejo**

**Objetivo:** Usar `useReducer` junto con `useContext` para manejar flujos asincrónicos y múltiples estados.

**Consigna:**

1. Crea un `AuthContext` que maneje:
    
    - Estado: `user`, `status: "idle" | "loading" | "success" | "error"`, `error`
        
2. Usa `useReducer` para manejar los distintos estados del login.
3. Expón una función `login(username, password)` que:
    
    - Simule una petición con `setTimeout`.
        
    - Modifique los estados adecuadamente (`loading` → `success` o `error`).
4. Crea componentes que:
    
    - Muestren el usuario si está logueado.
        
    - Muestren un spinner mientras se inicia sesión.
        
    - Muestren errores si hay fallos en la autenticación.