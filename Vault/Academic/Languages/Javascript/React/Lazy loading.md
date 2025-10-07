---
tags:
  - react
  - basic
---
---

##### Lazy loading en React

Técnica para cargar componentes solo cuando sea necesario, para mejorar el rendimiento, velocidad y SEO. 
Cuando usas lazy loading, React divide el código en **chunks**, y los carga dinámicamente en memoria solo cuando el usuario accede a la parte de la aplicación que los requiere

React ofrece soporte nativo para lazy loading a través de los helpers:

**`<Suspense>`** → Actúa como un _placeholder_ o componente de espera que se muestra mientras el componente lazy se está cargando
```ts
<Suspense fallback={<div>Cargando...</div>}>
  <MyComponent />
</Suspense>
```

**`React.lazy()`** → Permite cargar un componente de forma diferida
```js
const MyComponent = React.lazy(()=> import("./src/components/MyComponent.js"));
```

---

##### Ejemplo:

```js
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
import { Suspense, lazy } from 'react';
import Home from './pages/Home';

const Profile = lazy(() => import('./pages/Profile'));

export default function App() {
  return (
    <Router>
      <nav>
        <Link to="/">Inicio</Link> |{" "}
        <Link to="/profile">Perfil</Link>
      </nav>

      <Suspense fallback={<div>Cargando sección...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/profile" element={<Profile />} />
        </Routes>
      </Suspense>
    </Router>
  );
}
```

-  Cuando entras a la app (ruta `/`), **solo se carga `Home.jsx` y el código base**.
-  El componente `Profile` **no se descarga todavía**.
-  Cuando haces clic en “Perfil”, React descarga el **chunk de `Profile.jsx`** y lo muestra.
-  Mientras carga, aparece el mensaje del `<Suspense fallback="...">`.