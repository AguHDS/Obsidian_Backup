---
tags:
  - POO
  - basic
---

#### Que es?

El **polimorfismo** es la capacidad que tienen los objetos en la programación orientada a objetos para **responder de manera diferente al mismo método**, dependiendo de su clase específica.

Por ejemplo, podemos tener una clase `FiguraGeometrica` con un método `dibujar()`. Luego, podemos tener clases como `Triangulo`, `Cuadrado` o `Circulo` que heredan de `FiguraGeometrica` y **sobrescriben** el método `dibujar()` para que cada una lo implemente de forma distinta (dibujar un triángulo, un cuadrado o un círculo respectivamente).

Así, aunque llamemos al mismo método `dibujar()` sobre diferentes figuras, cada una se comportará según su propia implementación. Eso es polimorfismo.

---

#### Donde se aplica?

La idea de polimorfismo se aplica a lo largo de [[POO]].
Ejemplo con funciones de tipo generico en TS

```ts
function imprimir<T>(dato: T): void {
  console.log(dato);
}

imprimir("Hola");     // string
imprimir(123);        // number
imprimir(true);       // boolean
```
-  Aquí `imprimir()` funciona con **cualquier tipo** → es polimorfismo.

En lenguajes como [[Csharp]], se tiene que combinar esto con los modificadores [[virtual, override]] para permitir a clases hijas sobrescribir metodos