---
tags:
  - context
  - react
  - basic
---
links: [[react]], [[Context-Aware Component]]

---
### 📌 ¿Qué es el Context en React?

El **Context API** de React permite compartir datos entre componentes sin necesidad de pasar props manualmente a través de cada nivel del árbol.

Es ideal para:

-  Autenticación (usuario logueado)
-  Temas (oscuro/claro)
-  Idioma (i18n)
-  Estado global simple (sin Redux/MobX/etc)
-  Evitar prop drilling (pasar props entre multiples componentes hijos-nietos)

---

### ⚙️ Cómo crear un Contexto

#### 1. Crear el contexto
```tsx
// ThemeContext.tsx
import { createContext, useContext, useState } from "react";

type Theme = "light" | "dark";

const ThemeContext = createContext<{
  theme: Theme;
  toggleTheme: () => void;
} | null>(null);

export const ThemeProvider = ({ children }: { children: React.ReactNode }) => {
  const [theme, setTheme] = useState<Theme>("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// Custom hook
export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error("useTheme must be used within ThemeProvider");
  return context;
};
```

#### 2. Usar el provider
```tsx
// App.tsx
import { ThemeProvider } from "./ThemeContext";
import { Main } from "./Main";

export default function App() {
  return (
    <ThemeProvider>
      <Main />
    </ThemeProvider>
  );
}
```

#### 3. Usar el contexto en Main

```tsx
// Main.tsx
import { useTheme } from "./ThemeContext";

export function Main() {
  const { theme, toggleTheme } = useTheme();

  return (
    <div style={{ background: theme === "dark" ? "#333" : "#fff" }}>
      <button onClick={toggleTheme}>Cambiar Tema</button>
    </div>
  );
}
```


### ### 🧩 Principio de Responsabilidad Única en Contextos

> **"Un contexto debería hacer una sola cosa y hacerla bien"**

Cuando un contexto maneja múltiples tipos de datos (por ejemplo, autenticación, estado del carrito, y configuración del usuario), puede causar **re-renders innecesarios** en todos los componentes que usan el contexto (incluso si solo usan un solo estado)

#### 🔁 Problema
```tsx
const AppContext = createContext({
  user: { name: "", role: "" },
  isLoggedIn: false,
  theme: "light",
  toggleTheme: () => {},
});
```

Un componente que solo necesite manejar theme puede re-renderizarse si el estado de user o isLoggedIn cambia.
#### ✅ Solución: dividirlo en contextos diferentes

AuthContext.tsx → solo manejo de autenticación
ThemeContext.tsx → solo theme
UserContext.tsx → solo datos del usuario

Se dividen en tres partes, ya que el contexto inicial hacia el tres cosas a la vez

#### Resumen claro:
-  **Todos los hijos dentro del `<Provider>` no se re-renderizan automáticamente solo por estar dentro del árbol**, **solo aquellos que _usan_ ese contexto (con `useContext`, `Context.Consumer`, etc.)**.
-  **Pero** el re-render del `Provider` _puede_ hacer que sus hijos también se re-rendericen **si el componente padre también se vuelve a renderizar** y no estás memorizando los hijos o el valor del contexto correctamente.

Por ejemplo, aca se dividió el contexto en ThemeProvider y AuthProvider y se anida de forma que los componentes solo esten dentro de su respectivo provider.
De esta forma ComponenteHijo y ComponenteAuth no se re-renderizan si el Provider del otro se re-renderea
<div>
  <ThemeProvider>
    <ComponenteHijo />
  </ThemeProvider>

  <AuthProvider>
    <ComponenteAuth />
  </AuthProvider>
</div>
```tsx
<div>
  <ThemeProvider>
    <ComponenteHijo />
  </ThemeProvider>

  <AuthProvider>
    <ComponenteAuth />
  </AuthProvider>
</div>
```

⚠️ Lo contrario (ineficiente):
```tsx
<AuthProvider>
  <ThemeProvider>
    <ComponenteHijo />
    <ComponenteAuth />
  </ThemeProvider>
</AuthProvider>
```

-  Cada vez que cambia el estado de `AuthContext`, **se re-renderiza todo dentro del `AuthProvider`**, incluidos `ComponenteHijo` y `ThemeProvider`, **aunque no usen ese contexto directamente**.
-  Esos renders podrían causar recomputaciones o renders costosos si no están memoizados.

---
### ⚙️ UseMemo para optimización con contexto

Cuando se usa context en react, cada vez que el Proivder ser renderiza (por cualquier motivo) el/los values que le pasas a este se recrean, lo que puede provocar re-renders en los componentes hijos que usan este contexto, incluso si nada cambió.
useMemo sirve para evitar este problema cacheando el objeto que le pasmos al value en el provider, y hacer que solo se recree cuando cambia la dependencia (el valor)

Ejemplo
```tsx
const ThemeProvider = ({ children }: { children: React.ReactNode }) => {
  const [theme, setTheme] = useState<Theme>("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  // ❌ Este objeto se crea en cada render (aunque `theme` no cambie)
  const value = { theme, toggleTheme };
  
  // ✅ Solo se recrea el objeto si `theme` o `toggleTheme` cambian
  const value = useMemo(() => ({ theme, toggleTheme }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};
```

####  Entonces... ¿**`useMemo` se debe usar SIEMPRE en contextos?**

**No necesariamente.**
**es recomendable** usarlo **cuando querés evitar renders innecesarios**, **especialmente si**:

-  Hay **muchos componentes consumidores** del contexto.
-  Tu contexto **proporciona objetos o funciones** como valor.
-  Los componentes hijos **no deberían renderizarse si solo cambió otra parte del contexto**.
  
  🪶 ¿Cuándo **no es necesario** `useMemo`?
  - **El valor del contexto es un valor primitivo** (por ejemplo, un string o booleano
```tsx
createContext("light"); // ningún objeto creado, sin riesgo de referencia nueva
```


- **El contexto tiene un solo consumidor o es muy simple**:
    - Si no hay reusabilidad crítica, ni preocupación por performance.

- **Estás prototipando** o creando un contexto temporal donde no hay problema con los renders.

####  ¿Cuándo **sí es buena práctica usarlo**?

1.  **Pasás objetos como valor** (`{ theme, toggleTheme }`, `{ user, logout }`, etc.).
2. **Cuando una operación es costosa** (por ejemplo, cálculos pesados) y no querés repetirla si sus inputs no cambiaron.
3.  **El contexto agrupa varios estados/funciones**.
4.  **Tu app es mediana/grande** y querés evitar renders innecesarios.

`useMemo` **consume memoria** para guardar el valor anterior y hace una comparación de dependencias en cada render. No es "gratis"

---

### 🧪 Veredicto

| Situación                             | ¿`useMemo` recomendado?             |
| ------------------------------------- | ----------------------------------- |
| Valor primitivo (string, bool)        | ❌ No necesario                      |
| Objeto/función como valor complejos   | ✅ Sí, para evitar renders           |
| Muchos componentes usando el contexto | ✅ Sí, para evitar multiples renders |
| Calculo pesados, operaciones costosas | ✅ Sí, siempre                       |
No abuses de useMemo usandolo siempre.