---
tags:
  - POO
  - basic
---

## 🧱 ¿Qué es una clase?

En la programación orientada a objetos, una **clase** es una plantilla o molde para crear objetos. Define cómo será un objeto: qué datos tendrá (**propiedades**) y qué puede hacer (**métodos** o funciones).

> Piensa en una clase como los **planos de una casa**: no es la casa en sí, pero puedes construir muchas casas (objetos) con esos planos.

💻 Ejemplo de Clase en TypeScript
```ts
class Computadora {
  public precio: number;
  public motherboard: string;

  constructor(precio: number, motherboard: string) {
    this.precio = precio;
    this.motherboard = motherboard;
  }

  // Método protegido: solo accesible dentro de la clase o subclases
  protected usarComputadora() {
    console.log("Usando computadora con motherboard", this.motherboard);
  }

  // Método público que llama internamente al método protegido
  public encender() {
    this.usarComputadora();
  }

  // Método estático para comprar la computadora
  static buyComputer(pago: number): Computadora | null {
    const precioReal = 100;
    const motherboard = "Gigabyte";

    if (pago >= precioReal) {
      console.log("Computadora comprada con éxito.");
      return new Computadora(precioReal, motherboard);
    } else {
      console.log("No hay suficiente dinero para comprar la computadora.");
      return null;
    }
  }
}
```

👤 Clase Usuario
```ts
class Usuario {
  private compu: Computadora;

  constructor(compu: Computadora) {
    this.compu = compu;
  }

  public usar() {
    this.compu.encender(); // Uso controlado del objeto Computadora
  }
}

```

🧪 Ejecución del código
```ts
const miCompra = Computadora.buyComputer(101);

if (miCompra) {
  const usuario = new Usuario(miCompra);
  usuario.usar(); // ✅ salida: "Usando computadora con motherboard Gigabyte"
}

```

Note:
Al instanciar un nuevo objeto a partir de una clase, cuando accedemos a un metodo u propiedad desde ese objeto, estamos accediendo a los datos de este nuevo objeto, no a los de la clase anterior, ya que fue usada como plantilla para crear una copia de si para este objeto