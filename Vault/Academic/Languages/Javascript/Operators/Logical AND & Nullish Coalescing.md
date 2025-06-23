---
tags:
  - basic
  - javascript
  - operators
  - react
---

### `??` – Nullish Coalescing Operator

```js
const result = maybeNullOrUndefined ?? [];
```
Esto **retorna un array vacío solo si la izquierda es `null` o `undefined`**

### `&&` – Logical AND


```js
condition && doSomething()
``` 
Si `condition` es falsy (como `null`, `undefined`, `false`, `0`, `""`) → **retorna eso mismo (falsy)**.
Si es truthy → **retorna el resultado del segundo operador (`doSomething()`)**

---

### Usos en React

Ambos son muy usados en [[React]] para la parte del render y fallback de datos

#### ✅ ??
 `??` – Muy usado para fallback de datos (especialmente en props o rendering)
`??` es útil cuando mostrás algo que **puede ser `undefined` o `null`** y querés un valor por defecto, sin afectar valores falsy como `0` o `false`.

```jsx
<p>Nombre: {user.name ?? "Desconocido"}</p>
-
<img src={profilePic ?? defaultAvatar} />
-
<input defaultValue={formData.age ?? 18} />
```

#### ✅ &&
Muy usado para renderizado condicional. Uno de los patrones de render mas conocidos

```jsx
{isLoggedIn && <UserDashboard />}
-
{items.length > 0 && <ItemList items={items} />}
-
{error && <p className="error">{error}</p>}
```