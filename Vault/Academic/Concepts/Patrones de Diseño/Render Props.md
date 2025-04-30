---
tags:
  - react
  - Design_Patterns
  - medium
  - frontend
  - function_as_child
---
---

### 📌 Que es?

Patrón de diseño para [[React]]. En render props, un componente recibe una función como prop que retorna lo que se quiere renderizar.
La función recibe valores del componente padre (como estado o lógica) y retorna JSX personalizado.

📈 **Su uso bajó con la llegada de los hooks**
Este patrón se creó antes de los **Hooks**, y fue una solución común para reutilizar lógica entre componentes.

---

### 🎯 ¿Dónde se usa este patrón?

-  Cuando querés **reutilizar lógica de estado, eventos, ciclos de vida**, sin usar HOC.
-  Cuando el componente "padre" **controla un flujo lógico** (como carga de datos, mouse, tamaño de ventana), y **el componente hijo** se encarga de la presentación visual.

> **Ejemplo clásico:** un componente que detecta el movimiento del mouse y te da esas coordenadas para renderizar algo en base a ellas.


---

### ⚠️ ¿Cuándo NO usarlo?

-  Cuando podés resolverlo más fácil con **Hooks** (en React moderno, Hooks son preferidos)
-  Cuando no querés complicar la estructura de tus componentes
-  Cuando el nivel de anidación (nesting) se vuelve incómodo y difícil de leer

---

### 📋 Ejemplo

```tsx
function MouseTracker({ render }) {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handler = (e) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener("mousemove", handler);
    return () => window.removeEventListener("mousemove", handler);
  }, []);

  return render(pos);
}
```

uso
```tsx
<MouseTracker render={(pos) => ( 
  <p>Mouse en: {pos.x}, {pos.y}</p>
)} />
```

---

### 🧠 ¿Qué es **Function as Child**?

Es una **variante del patrón Render Props**, donde **la prop `children`** se utiliza como función en lugar de JSX tradicional.

MouseTracker.tsx
```tsx
export const MouseTracker = ({ children }) => {
  const [coords, setCoords] = React.useState({ x: 0, y: 0 });

  const handleMouseMove = (e) => {
    setCoords({ x: e.clientX, y: e.clientY });
  };

  return (
    <div onMouseMove={handleMouseMove} style={{ height: '200px', border: '1px solid gray' }}>
      {children(coords)}
    </div>
  );
};
```

uso
```tsx
<MouseTracker>
  {({ x, y }) => <h2>Mouse en ({x}, {y})</h2>}
</MouseTracker>
```

---

### Ejemplo realista de Render props

Componente de autenticación

AuthProvider.tsx
```tsx
import React, { useEffect, useState } from 'react';

// Tipo de usuario
type Usuario = {
  nombre: string;
  rol: string;
};

// Props del componente con render prop
interface AuthProviderProps {
  render: (user: Usuario | null) => React.ReactNode;
}

export const AuthProvider: React.FC<AuthProviderProps> = ({ render }) => {
  const [user, setUser] = useState<Usuario | null>(null);

  useEffect(() => {
    // Simula una carga de usuario desde backend
    const timeout = setTimeout(() => {
      setUser({ nombre: 'Agustín', rol: 'admin' });
    }, 1000);

    return () => clearTimeout(timeout);
  }, []);

  return <>{render(user)}</>;
};
```
🧠 Notas sobre tipado
- `Usuario` define el tipo del objeto `user`.
- `render` es una **función que recibe `user` y retorna JSX**, por eso su tipo es `(user: Usuario | null) => React.ReactNode`.

Uso en otro archivo (`App.tsx` por ejemplo)
```tsx
import { AuthProvider } from './AuthProvider';

function App() {
  return (
    <AuthProvider
      render={(user) =>
        user ? (
          <h1>Hola, {user.nombre}. Rol: {user.rol}</h1>
        ) : (
          <p>Cargando usuario...</p>
        )
      }
    />
  );
}

export default App;
```