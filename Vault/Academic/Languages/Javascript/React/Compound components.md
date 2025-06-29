---
tags:
  - frontend
  - react
  - Design_Patterns
  - medium
---
links: [[React]], [[Patrones de Diseño]]

---

### 📌 Que es?

Compound components es un patrón de diseño muy usado en [[React]] para el [[frontend]].
Este patrón consiste en **dividir un componente complejo en varios componentes pequeños** que funcionan juntos **de forma coordinada**, como si fueran "partes de un mismo organismo".

Se usa cuando tenemos componentes que dependen uno del otro: por ejemplo <MenuItem /> depende de <Menu /> para existir, y viceversa.

---
### ✅ Cuando usarlo?

**Úsalo cuando**:

-  Tienes dos o mas componentes que dependen uno del otro para existir.
-  Quieres construir componentes complejos (como formularios, tabs, menús, modales) que tengan varias partes **pero que estén conectadas entre sí**.
-  Buscas **evitar pasar props manualmente** a cada nivel (lo cual puede hacer el código muy sucio).

---
### ❌ Cuando NO usarlo

-  Componente con muchas partes independientes (ej. un perfil con secciones distintas y logica/estados propios)
-  Lógica compleja o entrelazada que no depende de estructura de hijos
-  Cuando props simples o hooks resuelven el problema con menos código

*Si querés que varios subcomponentes compartan comportamiento sin pasar props manualmente, y querés una API clara basada en JSX → Compound 
Component es útil.Si tus partes no se comunican entre sí o solo muestran datos → no lo uses.*

---

### 📋 Ejemplo

Vamos a aplicar Compund pattern para pestañas

1. Primero creamos un componente padre <Tabs />
```tsx
export function Tabs({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}
```

2. Ahora, abajo definimos sus subcomponentes, agregandolos como si fueran propiedades
```tsx
Tabs.List = function List({ children }: { children: React.ReactNode }) {
  return <div className="tabs-list">{children}</div>;
};

Tabs.Trigger = function Trigger({ value, children }: { value: string, children: React.ReactNode }) {
  return <button>{children}</button>;
};

Tabs.Content = function Content({ value, children }: { value: string, children: React.ReactNode }) {
  return <div>{children}</div>;
};
```

3. Podrias usarlo en cualquier lugar ahora, haciendo algo como esto:
```tsx
<Tabs>
  <Tabs.List>
    <Tabs.Trigger value="tab1">Tab 1</Tabs.Trigger>
    <Tabs.Trigger value="tab2">Tab 2</Tabs.Trigger>
  </Tabs.List>

  <Tabs.Content value="tab1">
    Contenido de la Tab 1
  </Tabs.Content>
  <Tabs.Content value="tab2">
    Contenido de la Tab 2
  </Tabs.Content>
</Tabs>
```

Qué pasa acá?

**Todos los componentes internos** (`List`, `Trigger`, `Content`) funcionan **gracias al contexto** que el `Tabs` principal les ofrece, **sin necesidad de que tú como desarrollador tengas que pasar manualmente estados o props**.

---
Ejemplo con accordion para desplazarlo

```tsx
const AccordionContext = createContext();

export const Accordion = ()=> {
    const [activeIndex, setActivateIndex] = useState(null);

    return (
        <AccordionContext.Provider value={{activeIndex, setActivateIndex}}>
        return <div>{children}</div>
        </AccordionContext.Provider>
    )
}

//aca entra la logica de compound components, agregandole propiedades como subcomponentes
Accordion.Item = function AccordionItem({title, children, index})=> {
    const {activeIndex, setActivateIndex} = useContext(AccordionContext);
    const isActive = activeIndex === index;

    const toogle = ()=> {
        setActivateIndex(isActive ? null : index);
    }

    return(
       <div className="accordion-item">
        <div onClick={toggle} >
        {title}
        </div>
        {isActive && (<div>{children}</div> )}
    </div>
    )
}
```

Uso en algun lado de la app
```tsx
import { Accordion } from "./components/Accordion";

export const App = () => {
    return (
        <Accordion>
         <Accordion.Item title="Primer titulo" index={0}>Info del primer                     titulo</Accordion.Item>
         <Accordion.Item title="Segundo titulo" index={1}>Info del Segundo                     titulo</Accordion.Item>
         <Accordion.Item title="Tercer titulo" index={2}>Info del Tercer                     titulo</Accordion.Item>
        </Accordion>
    )
}
```

