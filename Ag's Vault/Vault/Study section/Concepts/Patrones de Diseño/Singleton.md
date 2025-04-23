---
tags:
  - POO
  - basic
---
---

Patrón de diseño de la programación orientada a objetos
El patrón **Singleton** asegura que una **clase tenga una única instancia** en todo el sistema, y que esa instancia sea fácilmente accesible. Sirve cuando **no tiene sentido tener más de una instancia** (por ejemplo, una conexión a base de datos, un logger, una configuración global, etc.).

#### Cómo funciona?

1. La clase crea su única instancia
2. Oculta su constructor para que nadie más pueda crear objetos directamente
3. Nunca hay que instanciar un nuevo objeto a partir de la clase (ni podrias, porque el constructor es privado), en su lugar, expone un método como `getInstance()` que devuelve esa única instancia

---
#### ✅ ¿Cuándo se usa?

-  Solo tiene sentido tener una única instancia de algo
-  Necesitas **coordinación global** (por ejemplo, que todos los módulos usen el mismo logger)
-  Quieras evitar crear múltiples instancias costosas o con estados compartidos

##### Se usa mucho? Cuando?

Sí, se usa para:
- **Conexiones a bases de datos** (como Mongoose con MongoDB en Node)
- **Servicios compartidos**, como loggers
- **Gestión de configuración** global
- **Clientes de APIs externas**, que solo necesitas instanciar una vez

#### ❌ ¿Cuándo no usarlo?

-  En aplicaciones grandes, **abusar del Singleton puede generar dependencias ocultas y dificultad para testear**.
-  Se considera un **antipatrón** en algunos contextos por romper el principio de inversión de dependencias y hacer que el código dependa de "cosas globales".


---

### Cómo se implementa en TypeScript?

```ts
// Logger.ts
export class Logger {
  private static instance: Logger; //instancia de la clase

  private constructor() {
    // Constructor privado para evitar instanciación externa
    console.log('Logger creado');
  }

  public static getInstance(): Logger {
    //verificar si la instancia de la clase existe, si no, crear una
	//esto es para asegurar que se pueda instanciar solo una única vez
    if (!Logger.instance) {
      Logger.instance = new Logger();
    }
    return Logger.instance;
  }

  public log(message: string): void {
    console.log(`[LOG]: ${message}`);
  }
}

```

Uso:
```ts
// index.ts
import { Logger } from './Logger';

const logger1 = Logger.getInstance();
logger1.log("Primer mensaje");

const logger2 = Logger.getInstance();
logger2.log("Segundo mensaje");

console.log(logger1 === logger2); // true: es la misma instancia

```

---

### Por que usar static en singleton?

Si no fuera `static`, tendrías que crear una instancia de `Logger` **para acceder a `instance`**, lo cual **rompería el propósito del Singleton**
Al ser `static`, puedes acceder a ella directamente con `Logger.instance`, y evitar que se haga lo que quiera desde otra parte del codigo, por eso se usa **private static** y no public static, o public.

###  📋 Gráfico

| **Modificador**  | **¿A qué pertenece?**      | **¿Visible desde fuera?** | **¿Comparte entre instancias?** | **¿Sirve bien para Singleton?**                |
| ---------------- | -------------------------- | ------------------------- | ------------------------------- | ---------------------------------------------- |
| `private`        | A cada instancia           | ❌ No                      | ❌ No                            | ❌ No (no se puede acceder sin crear instancia) |
| `private static` | A la clase (no instancias) | ❌ No                      | ✅ Sí                            | ✅ **Sí (ideal, asegura control total)**        |
| `public`         | A cada instancia           | ✅ Sí                      | ❌ No                            | ❌ No (requiere instanciar primero)             |
| `public static`  | A la clase (no instancias) | ✅ Sí                      | ✅ Sí                            | ❌ No (**rompe el control de acceso**)          |