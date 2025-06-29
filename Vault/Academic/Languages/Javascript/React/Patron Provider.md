---
tags:
  - react
  - Design_Patterns
  - advanced
---
links: [[React]], [[Patrones de Diseño]], [[Context]]

---

#### 🧩 Que es?

El "Patron Provider" es una forma en la que se trabaja con context api y se soluciona un problema de arquitectura: el propagar logica evitando [[prop drilling]].

---
#### Como se aplica?

-  Cuando creas un contexto, defines una función llamada AlgoProvider, esta función va a hacer uso de childrens como prop para meter dentro los componentes que necesitan este contexto.
-  Dentro de esta función, defines logica que los componentes necesiten usar, y los pasas como un objeto en la prop value dentro del return en AlgoContext.Provider

Ejemplo:
```tsx
const ThemeContext = createContext("light");

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

Ahi se aplica ThemeProvider (patron provider), porque  <ThemeContext.Provider  /> es lo que se usa en este patron de diseño. En las malas practicas no se suele usar, y se usa directamente ThemeContext, porque:
**const ThemeContext = createContext("light");** <- ya con esto podes usar el contexto 

---

#### ✅ Cuando usar?

Usalo cuando:

-  Tenés **estados o lógica globales** que necesitás en muchos componentes.
-  Querés **evitar prop drilling**.

-  Estás manejando cosas como:
    
    -  Usuario autenticado
    -  Tema (oscuro/claro)
    -  Preferencias de idioma
    -  Carrito de compras
    -  WebSockets, conexión global, etc.

---

#### Y si tengo muchos Providers?

Se debería hacer un componente envolvedor de Providers, para que no se aniden. una práctica común es **envolver todos en un único archivo que los agrupe**, como:

```tsx
export const AppProviders = ({ children }) => {
  return (
  // Estos providers tienen que estar disponibles para TODA la app
    <ThemeProvider>
      <AuthProvider>
        <SocketProvider>
          {children}
        </SocketProvider>
      </AuthProvider>
    </ThemeProvider>
	  );
};
```

Y luego:
```jsx
<AppProviders>
  <App />
</AppProviders>
```

Esto se **aplica** cuando:
-  **Los contextos están anidados en el mismo lugar**, típicamente en el `main.tsx`, `App.tsx`, o `Layout.tsx`
-  No quiere decir que metas los Providers de todo el proyecto ahi, no tendria sentido hacer eso
#####  Por qué hacerlo así?

-  Porque evita tener `<ThemeProvider><AuthProvider><SocketProvider>...` disperso por el código.
-  Mejora la **legibilidad**.
-  Centraliza los proveedores en un solo lugar: `AppProviders.tsx`.



---

#### 🧭 ¿Cuándo NO usar Context + Provider?

-  Si solo necesitás compartir datos entre 2-3 componentes cercanos, podés usar props sin problema
-  Si tu estado cambia **muy seguido y rápidamente**, usar context puede hacer que muchos componentes se re-rendericen. En ese caso, conviene **Redux**, Zustand o incluso mover el estado más cerca de los componentes