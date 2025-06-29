---
tags:
  - react
  - basic
---
links: [[Bases de React (advanced)]]

---

#### Que es SSR?

SSR es hacer el **renderizado en el lado del servidor**. Esto hace que se renderice el DOM en el backend y lo envie ya renderizado en el servidor. Esto ayuda al SEO porque el contenido esta listo en una primera instancia.

Beneficios:
-  Mejor **SEO** (los bots ven el contenido ya cargado).
-  Mejora el **Time to First Paint**.
-  Útil para contenido dinámico que debe estar indexado.

Para esto se suele trabajar con Next.js

##### 🌐 **Routing en SSR**

-  No se usa React Router como en CSR puro.
-  En Next.js, el enrutado es basado en archivos (`pages/`).

##### 📄 **SEO con SSR**

-  SSR permite que el contenido ya esté renderizado para bots.
-  Uso correcto de `<Head>`, `<title>`, `<meta>` tags.
-  En Next.js: `next/head`.

##### 🍪 **Autenticación**

-  Tokens o sesiones se deben manejar en el servidor (ej. cookies HTTP-only).
-  Middleware y redirecciones desde `getServerSideProps`.

---

#### Preguntas 

##### Cuál es la diferencia entre SSR y SSG?

| SSR (Server-Side Rendering)                                                   | SSG (Static Site Generation)                                |
| ----------------------------------------------------------------------------- | ----------------------------------------------------------- |
| Se renderiza el HTML **en cada request** del usuario.                         | El HTML se genera **una vez en build time**.                |
| Ideal para contenido **dinámico** o personalizado (ej: dashboard de usuario). | Ideal para contenido **estático** (ej: landing page, blog). |
| Más carga en el servidor.                                                     | Carga muy rápida y más fácil de cachear en CDN.             |

##### Cómo manejarías un token JWT en SSR?

Es muy parecido a cuando lo haces en express

En SSR no hay `localStorage`, así que se usa **cookies HTTP-only**:

1.  El servidor lee el token desde `req.cookies`.
2.  Verifica el JWT (ej. con `jsonwebtoken.verify()`).
3.  Si es válido, puedes obtener los datos del usuario.
4.  Si no es válido, rediriges al login.

##### **Qué pasa si hay una diferencia entre el render del servidor y del cliente?**

Esto se llama un **"hydration mismatch"**.

-  El servidor genera un HTML diferente al que React espera al hidratar en el cliente.
-  Esto causa advertencias como:
    > "Text content did not match. Server: 'Hello' Client: 'Hola'"

**Causas comunes:**

-  Código que depende de `window`, `localStorage`, fechas o IDs aleatorios (`Math.random()`, `Date.now()`)
-  Estado no sincronizado entre cliente y servidor

**Solución:** Usar `useEffect` para evitar código no determinista en el render del servidor. Mas conocido como Hydration mismatch

	La solución al _hydration mismatch_ consiste en **evitar que el HTML generado en el servidor sea diferente del que genera el cliente en el primer render**.

##### Por qué usar `useEffect` lo soluciona?

Porque `useEffect`:

-  **No se ejecuta en el servidor**
-  Solo corre **después de que el componente ya está montado en el cliente**

Entonces:

1.  En el SSR (Server-Side Rendering), React **no ejecuta `useEffect`**, así que no se llama a `Date.now()` ahí.
2.  En el cliente, React hidrata usando el HTML generado por el servidor (sin ejecutar aún `useEffect`)
3.  Luego de hidratar, **se ejecuta `useEffect`**, y ahí podés actualizar el estado con `Date.now()`