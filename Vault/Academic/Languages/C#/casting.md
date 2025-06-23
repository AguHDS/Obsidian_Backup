---
tags:
  - POO
  - Csharp
  - basic
---
#### Que es casting?
Es **convertir o forzar un valor u objeto a otro tipo compatible.**  
Se usa cuando vos sabés que el valor realmente puede comportarse como el nuevo tipo.

---
#### Como hacer casting?

El casting en C# se puede hacer de varias formas, dependiendo de las necesidades

#####  1. **Casting explícito (entre tipos compatibles)**

Cuando los tipos son compatibles pero la conversión puede **perder información**, necesitas usar **casting explícito** usando paréntesis.

```csharp
double decimal = 9.8;
int entero = (int)decimal //se pierde la parte decimal, ahora es 9
```

##### 2. Usar as para casting "seguro"

Retorna `null` si no puede convertir

```csharp
object obj = "hola";
string str = obj as string;  // str será "hola"

object obj2 = 123;
string str2 = obj2 as string;  // str2 será null
```

##### 3. Casting con tipos base y derivados (herencia)
Cuando trabajás con clases y querés convertir de una clase base a una clase hija (o viceversa):
```csharp
class Animal { }
class Perro : Animal { }

Animal a = new Perro();        // Implícito (hacia la clase base)
Perro p = (Perro)a;            // Explícito (hacia la clase derivada)
```

##### Nota: Usar is para verificar antes de castear

```csharp
if (obj is string texto)
{
    Console.WriteLine("Es un string: " + texto);
}
```

Cuando usar casting explicito (paso 1) o "as"?

| Quiero…                                         | Uso recomendado      |
| ----------------------------------------------- | -------------------- |
| Lanzar excepción si no es el tipo esperado      | `(Tipo)obj`          |
| Evitar excepción y chequear con `null`          | `obj as Tipo`        |
| Verificar el tipo antes de castear (C# moderno) | `if (obj is Tipo t)` |
-  Usa **`(Tipo)`** si querés que se **detenga con error** si el tipo no coincide
-  Usa **`as`** si querés **evitar errores** y manejar el caso con un `if (obj != null)`