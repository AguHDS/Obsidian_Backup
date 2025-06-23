---
tags:
  - Csharp
  - POO
  - polimorfismo
---
Los modificadores `virtual` y `override` se usan en lenguajes orientados a objetos como **C#**, **C++** o **Java (con otras palabras)**, y tienen que ver **directamente con el [[Polimorfismo]]**.  

---

### 🧩 Para qué sirve virtual?

-  Se usa en una **clase base (padre)** para indicar que un **método puede ser sobrescrito** por las clases hijas
-  Si un método **no es `virtual`**, no se puede sobrescribir con `override`

```csharp
class Figura {
    public virtual void Dibujar() {
        Console.WriteLine("Dibujar figura genérica");
    }
}
```
Con `virtual`, decimos: “las clases hijas pueden redefinir esto si quieren”

---
### 🧩 Para qué sirve override?

-  Se usa en una **clase derivada (hija)** para **sobrescribir** un método virtual de la clase base
-  Así, la nueva versión reemplaza la original al ejecutarse

```csharp
class Triangulo : Figura {
    public override void Dibujar() {
        Console.WriteLine("Dibujar triángulo");
    }
}
```

