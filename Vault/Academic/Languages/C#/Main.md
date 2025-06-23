---
tags:
  - Csharp
  - POO
  - basic
---
#### Clase principal "Main"

En C#, el código **no puede estar suelto**. Todo **debe vivir dentro de una clase o método**. Y cuando **el programa se ejecuta**, busca un **punto de entrada** llamado:
```csharp
static void Main(string[] args)
```
> Ese `Main` es como el punto de partida del programa. Si no está, **no sabe por dónde empezar**

Lo ideal por convencion es tener una clase Program, que contenga ese metodo Main, y ubicarlo al final del archivo o crear un archivo Program.cs para eso, Main va a tener la parte que configura y “arranca” el servidor web.

`Program.cs` solo configura la aplicación y la infraestructura, no contiene lógica de negocio ni modelos.

Entonces, en aplicaciones web complejas con .NET:
Hay archivos `Program.cs` y `Startup.cs` que configuran la app, el servidor y rutas.  
Pero la ejecución no es una secuencia de instrucciones en `Main`, sino un servidor que espera peticiones.

⚠️ Importante: en C#, **los nombres de métodos y clases van en mayúscula** por convención

### Y si tu código crece mucho en apps web complejas?

-  Podés dividir la lógica en muchos métodos y clases.
-  El `Main` sigue siendo el inicio, pero delega a otros métodos o clases que instancian lo necesario.
-  Por ejemplo, crear un método `InicializarObjetos()` que haga las instancias, y desde `Main` llamás a ese método.
  
```csharp
class Program
{
    static void Main(string[] args)
    {
        InicializarObjetos();
    }

    static void InicializarObjetos()
    {
        Empleado emp = new Empleado(30, "Ana");
        Fabrica fab = new Fabrica(emp, new int[] {1, 2, 3});
        
        fab.metodoRandom();
    }
}
```


#### Archivo de prueba simple (app de consola)

Program.cs
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Empleado trabajador = new Empleado(14, "juan");
        Fabrica fabrica = new Fabrica(trabajador, new int[] { 6, 2, 4, 8 });
        fabrica.MetodoRandom();
    }
}
```

Empleado.cs
```csharp
public class Empleado
{
    public int Edad { get; };
    public string Nombre { get; };

    public Empleado(int edad, string nombre)
    {
        Edad = edad;
        Nombre = nombre;
    }

    public string hablar()
    {
        return "Hola, soy un empleado";
    }
}
```

Fabrica.cs
```csharp
public class Fabrica
{
    Empleado trabajador;
    int[] sectores;

    public Fabrica(Empleado trabajador, int[] sectores)
    {
        this.trabajador = trabajador;
        this.sectores = sectores;
    }

    public void MetodoRandom()
    {
        Console.WriteLine(trabajador.Hablar() + ", mi nombre es " + trabajador.Nombre);
    }
}
```