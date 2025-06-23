---
tags:
  - react
  - context
  - basic
---
tags: [[context]], [[react]]

---

####  Qué es?

Es una técnica en la que se crea un nuevo componente cuando se necesita que parte de la lógica se use dentro de un [[context]]. Puede haber casos donde la logica que usa el contexto esta en el cuerpo del componente, y el Provider en el jsx, eso es un problema porque se usa el contexto fuera del Provider.

---

#### 📦 Cuándo usarlo

-  Cuando **tienes lógica o hooks que usan un contexto**, pero el contexto aún **no está disponible en ese punto del árbol**
-  Cuando no quieres mover el `Provider` a un nivel más alto
-  Cuando quieres **aislar la lógica consumidora** del contexto

---

#### 🧩 Ejemplo básico

```tsx
// ProfileContext.tsx
const ProfileContext = createContext(null);
export const useProfile = () => useContext(ProfileContext);
```

```tsx
// App.tsx
import { ProfileProvider } from './ProfileContext';

export default function App() {
  return (
    <ProfileProvider>
      <MainComponent />
    </ProfileProvider>
  );
}
```

```tsx
// MainComponent.tsx
export function MainComponent() {
  return (
    <ProfileProvider>
      <ProfileLogic /> {/* Aquí va la parte que usa el contexto */}
    </ProfileProvider>
  );
}

// Este componente sí puede usar el contexto porque está dentro del Provider
function ProfileLogic() {
  const profile = useProfile();

  return <div>{profile.name}</div>;
}
```

Piensa que en un principio, el codigo de <ProfileLogic /> estaba en el cuerpo de MainComponent(). Pero como se usaba codigo del contexto ProfileProvider, fue necesario crear <ProfileLogic /> y meterlo dentro del contexto.
Este tipo de archivos puede ir en: **src/features/profile/containers** (usando featured-based architecture), ya que no es un componente de UI puro, sino un componente de logica para un contexto especifico.

---

#### 🔥 Mejores prácticas

-  Nombrá claramente el subcomponente (`<WithProfileData />`, `<ProfileConsumerComponent />`, etc.)
-  No abuses de esta técnica si puedes colocar el Provider a un nivel superior
-  Evitá que el subcomponente sea muy grande: **extraé hooks o lógica reutilizable** si es necesario