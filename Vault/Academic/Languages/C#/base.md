---
tags:
  - Csharp
  - POO
---
#### 🧩 Para qué sirve base?

**Base** es una palabra reservada en [[Csharp]], se usa para acceder a metodos, propiedades, [[Constructor]] de la clase padre desde la clase hija. Es el equivalente a usar [[super]] en [[Typescript]]

Ejemplo:
```csharp
class Persona() {
	public string Nombre

	public Persona(string nombre) {
		Nombre = nombre;
	}
}

class Employer : Persona {
	public Employer(string nombre) : base(nombre) {
		//llama al constructor de Persona
	}
}
```

##### Por qué en `Employer` se necesita llamar al constructor del padre (`Persona`)?

Porque **cuando creás un objeto de tipo `Employer`**, internamente se necesita construir **también la parte `Persona`** que forma parte del objeto.
Aunque `Employer` **hereda** de `Persona`, **la construcción del objeto `Persona` no ocurre automáticamente sin llamar a su constructor**.

📌 En C#, los constructores **no se heredan**

📌 Cuando defines un constructor en una clase hija, si la clase base no tiene un constructor vacío (sin parámetros), **tienes que llamar explícitamente a un constructor del padre** para inicializar sus propiedades.

La palabra clave base llama al constructor del padre para asegurarte de que las propiedades del padre y las nuevas del hijo esten inicializadas, y puedas instanciar un objeto a partir de esta clase hija.

---

####  **Acceder a métodos o propiedades de la clase padre**

Se puede usar para acceder a metodos o propiedades de la clase base

```csharp
class Animal {
    public virtual void HacerSonido() {
        Console.WriteLine("Sonido genérico");
    }
}

class Perro : Animal {
    public override void HacerSonido() {
        base.HacerSonido(); // Llama a la versión del PADRE
        Console.WriteLine("Guau!");
    }
}
```

##### Por qué se usa `base.HacerSonido()` y no `this.HacerSonido()`?

La diferencia clave es:

-  **`this.HacerSonido()`** llama al método actual, es decir, **el que fue sobrescrito en la clase hija**
-  **`base.HacerSonido()`** llama al método de la **clase padre** que fue sobrescrito

Si usaramos this.HacerSonido dentro del metodo HacerSonido de la clase Perro, causaría un bucle infinito porque estaria llamando constantemente al metodo HacerSonido de la clase hija, gracias a this. Pero actualmente se accede al del padre (un metodo diferente).

#### Cuando no usar base?

Cuando la clase padre tiene un constructor sin parametros.
Si la clase padre tiene un **constructor por defecto (sin argumentos)**, **el compilador lo llama automáticamente**, así que no hace falta que escribas `base()`

💡 **Usá `base` solo cuando querés interactuar explícitamente con el padre.**

Si no tenés una razón específica para llamar al constructor del padre o a sus métodos/propiedades, **no lo uses, y el compilador se encargará por vos (cuando sea posible)**.

Ejemplo:

```csharp
class Animal {
    public Animal() {
        Console.WriteLine("Animal creado");
    }

    public virtual void Hablar() {
        Console.WriteLine("Sonido genérico");
    }
}

class Gato : Animal {
    public override void Hablar() {
        Console.WriteLine("Miau"); // No se usa base porque no necesito llamar al padre
    }
}

class Perro : Animal {
    public override void Hablar() {
        base.Hablar(); // Sí uso base porque quiero que diga primero el sonido genérico
        Console.WriteLine("Guau");
    }
}
```


Leelo asi para entenderlo:
```csharp
public class Persona {
    public string Nombre;

    public Persona(string nombre) {
        Nombre = nombre;
    }
}

public class Empleado : Persona {
    public Empleado(string nombre) : base(nombre) {
        // Aquí puede ir lógica adicional específica de Empleado
    }
}
```
👉 “Cuando alguien cree un `Empleado` y me dé un `nombre`, yo (`Empleado`) se lo voy a pasar al constructor de mi clase base (`Persona`) para que lo inicialice”.