---
tags:
  - backend
  - basic
---
## 🧩 Diferencias entre `query params` y `req.params`

|Concepto|`req.params`|`req.query`|
|---|---|---|
|📍 Tipo de dato|**Route parameters** (parámetros en la URL)|**Query parameters** (parámetros de consulta)|
|📌 Ubicación en URL|Parte de la ruta definida con `:` en el path|Después del `?` en la URL (como pares clave-valor)|
|🛣 Ejemplo de URL|`/user/123`|`/search?keyword=node&page=2`|
|🧪 Ejemplo de uso|`app.get("/user/:id", ...)`|`app.get("/search", ...)`|
|📤 Cómo acceder|`req.params.id`|`req.query.keyword`, `req.query.page`|

---

### Ejemplo de query params y req params enviados desde el frontend


username es un req.params (path params) y sectionId es un query param
```js
const response = await fetch(

        `http://localhost:${import.meta.env.VITE_BACKENDPORT}/api/uploadFiles/${username}?sectionId=${sectionId}`,
```

podriamos agregar mas query params poniendo "&" y repitiendo lo mismo, para trabajar con mas datos