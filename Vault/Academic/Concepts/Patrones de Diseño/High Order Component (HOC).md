---
tags:
  - react
  - frontend
  - Design_Patterns
  - medium
---
### 📌 ¿Qué es?

Un **High Order Component** es un **patrón de diseño** para el [[frontend]] muy usado en [[React]] donde:

-  Una función **recibe un componente** como argumento.
-  **Devuelve un nuevo componente** que envuelve, extiende o modifica el componente original.

Es una técnica para **reutilizar lógica** entre componentes de forma funcional y elegante, **sin tener que duplicar código**.

> **Idea general:** Así como en programación funcional existen **higher-order functions** (funciones que toman o devuelven funciones), en React tenemos **higher-order components**.

Hoy en dia esta en desuso porque se usan en su lugar context API y hooks (useEffect, useContext, useMemo, etc) para evitar cosas como wrapper hell.
Aunque se siguen usando para cosas como logging, tracking, permisos. Es un patrón de diseño de la programación funcional y sigue siendo valido.

Ejemplo, en este HOC, se se editan props, ni agrega funcionalidad, solo **va a decidir si renderizar el componente o no, dependiendo de si 'usuario' esta en localstorage.**
Podemos usar este HOC para decidir cuando renderizar un componente que requiera autenticación.
```ts
const withAuth = (WrappedComponent) => {
  return function EnhancedComponent(props) { <- //ProtectedComponent nace en este      punto, es el resultado de usar withAuth(MyComponente)
    const usuario = localStorage.getItem('usuario');
    if (!usuario) {
      return <p>Acceso denegado</p>;
    }
    
    //Este seria el componente que fue pasado al hoc
    return <WrappedComponent {...props} />; 
  };
};

//uso
const PanelPrivado = ({ nombre }) => {
 return <h1>Bienvenido al panel, { nombre }</h1>;
};

const ProtectedPanel = withAuth(PanelPrivado);

//en algun lado:
<ProtectedPanel nombre="Agustin" />
```

**Nota**
Es probable que visualmente te maree el flujo, sobre todo con el uso de props en EnhancedComponent. Pensálo asi:

1.  **En este punto no existen props todavía.**
-  Solo creamos y guardamos **el nuevo componente** (`EnhancedComponent`).
-  Dentro de este nuevo componente **ya se definió**:
    -  Si no hay usuario, muestra `<p>Acceso denegado</p>`.
    -  Si hay usuario, muestra `<WrappedComponent {...props} />`.
    -  No se ejecuta ningun render todavía, solo definimos que pasará cuando se renderice

```ts
const ProtectedPanel = withAuth(PanelPrivado);
```

2. **Ahora React sí ejecuta** `EnhancedComponent`.
-  React pasa `{ nombre: "Agustin" }` como `props` a `EnhancedComponent`.
- `EnhancedComponent`:
    -  Verifica si hay usuario en `localStorage`.
    -  Si todo bien, hace `<WrappedComponent {...props} />`, pasando `{ nombre: "Agustin" }` al `PanelPrivado`.
    - Ahi es donde las props finalmente existen y son usadas dinámicamente

```ts
<ProtectedPanel nombre="Agustin" />
```

# 🔥 Explicación aún más detallada

**JSX → Compilado a JavaScript**

| Lo que escribimos                     | Lo que realmente pasa internamente                           |
| ------------------------------------- | ------------------------------------------------------------ |
| `<ProtectedPanel nombre="Agustin" />` | `React.createElement(ProtectedPanel, { nombre: "Agustin" })` |

**Qué hace `React.createElement`:**
- Llama a `ProtectedPanel` como si fuera una función, pasando `{ nombre: "Agustin" }` como argumento.

Por eso, en `ProtectedComponent(props)`, `props` automáticamente es `{ nombre: "Agustin" }`

**"JSX es azúcar sintáctico. React transforma `<Componente prop=valor />` en una llamada de función `Componente({prop: valor})` automáticamente."**

---
### ❌ Cuando NO usar HOC?

Evítalo si:

-  El patrón añade **complejidad innecesaria** (un simple Hook o componente contenedor podría bastar).
-  Hay **problemas de anidamiento profundo** (muchos HOCs encimados dificultan el debug).
-  Estás usando **React moderno** y puedes resolverlo mejor con **Hooks** (`useEffect`, `useContext`, `useCustomHook`, etc).

> Hoy en día, los **Hooks** son la forma preferida para compartir lógica reutilizable en React

---

### 🧠 Cosas relevantes que debes saber

**Nombre de componentes:** Es buena práctica que el HOC copie el nombre del componente original para ayudar al debug:
```jsx
EnhancedComponent.displayName = `WithLogger(${WrappedComponent.displayName || WrappedComponent.name || 'Component'})`;
```

- **No modifiques el WrappedComponent directamente.** Los HOCs deben ser **puros**: solo recibir y devolver, sin alterar.

-  **Problemas comunes:**
    -  **Prop collision:** El HOC podría accidentalmente sobrescribir props del componente original.
    -  **Ref forwarding:** Los HOCs normales no reenvían automáticamente los `ref`. Se necesita usar `React.forwardRef` si quieres manejar `ref` correctamente.

---

## 📋 Resumen Visual

| Concepto                | HOC                                             |
| ----------------------- | ----------------------------------------------- |
| ¿Qué hace?              | Recibe un componente, devuelve otro             |
| ¿Para qué?              | Compartir lógica, extender funciones            |
| ¿Alternativas modernas? | Hooks                                           |
| ¿Problemas si abusas?   | Complejidad, nesting difícil de leer            |
| Inspiración             | Higher-order functions (programación funcional) |
