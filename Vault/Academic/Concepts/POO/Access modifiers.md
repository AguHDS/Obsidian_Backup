---
tags:
  - basic
  - POO
  - access_modifiers
  - private
  - protected
  - public
  - readonly
  - static
---

# 🔐 Modificadores de Acceso en TypeScript

Los modificadores de acceso controlan **quién puede acceder** a las propiedades y métodos de una [[Class]]. Esto permite proteger los datos y mantener la lógica interna encapsulada.

En TypeScript, los principales son:

##### public
1. 
	-  **Accesible desde cualquier parte**: dentro de la clase, desde clases hijas, y desde fuera de      la clase también. Incluso desde otros archivos si la clase es exportada
	-  Ideal para datos sensibles que no deben ser modificados directamente


--------

##### private
1.
	-  **Accesible solo desde dentro de la misma clase.**
	-  *No se puede acceder desde fuera, ni siquiera desde una clase que herede.*
	-  Para acceder a algo privado, puedes crear un método publico en la clase, que retorne esas cosas privadas.
	- Por convención, algunos devs usan el "_" para las propiedades privadas: _nombre
	- Un objeto instanciado de una clase no hereda sus propiedades privadas.
	

Ejemplo:
```ts
	class CuentaBancaria {
	  private saldo: number;
	
	  constructor() {
	    this.saldo = 1000;
	  }
	
	  public consultarSaldo() {
	    return this.saldo;
	  }
	}
	
	const cuenta = new CuentaBancaria();
	console.log(cuenta.consultarSaldo()); // ✅ OK
	// console.log(cuenta.saldo);         // ❌ Error: 'saldo' es privado
	
```

---

##### protected
3. 
	-  **Accesible desde la misma clase y clases hijas.*
	-  No se puede acceder desde fuera, **pero sí desde clases que heredan**. Tiene que si o si ser una clase que extienda la que tiene el método protected, no va a funcionar con una interface, por ejemplo
	
  
  Ejemplo:
```ts
class Empleado {
  protected puesto: string;

  constructor(puesto: string) {
    this.puesto = puesto;
  }
}

class Gerente extends Empleado {
  mostrarPuesto() {
    console.log(`Soy un gerente y mi puesto es ${this.puesto}`); // ✅ 
  }
}

const g = new Gerente("Jefe de Proyecto");
g.mostrarPuesto(); // ✅ Funciona
// console.log(g.puesto);           // ❌ Error: 'puesto' es protegido

```

---------

#### static
4. 
	-  Hace que una propiedad o método pertenezca **solo a la clase**, no a la instancia de objetos que se crean de ella
	-  Si algo es `static`, no es heredado desde objetos nuevos instanciados
	-  Las clases hijas si pueden heredar cosas static si extienden
	-  podés usarlo directamente llamando a la clase, **sin crear un objeto**
	 
  Ejemplo:
```ts
class Contador {
  static cantidad: number = 0;

  constructor() {
    Contador.cantidad++; // se accede directamente desde la clase
  }

  static mostrarCantidad() {
    console.log(`Se crearon ${Contador.cantidad} objetos`);
  }
}

Contador.mostrarCantidad();
```

```ts
class Base {
  static saludo = "Hola desde Base";
}

class Hija extends Base {}

console.log(Hija.saludo); // ✅ Funciona, se hereda como clase

```

##### 🧠 ¿Qué podés hacer con `static`?

| Uso                 | Ejemplo breve                              |
| ------------------- | ------------------------------------------ |
| Contadores globales | Contar cuántas instancias se crearon       |
| Métodos utilitarios | Funciones de ayuda sin necesidad de objeto |
| Constantes de clase | Valores fijos relacionados a la clase      |

-----

#### readonly
5. 
	-  sirve para hacer una propiedad inmutable una vez su valor dado, no se puede cambiar
	-  se puede asignar una unica vez, al declararla o en el constructor.

✅ ¿Cómo se usa?
```ts
class Libro {
  readonly titulo: string;

  constructor(titulo: string) {
    this.titulo = titulo; // ✅ Se puede asignar aquí
  }

  cambiarTitulo() {
    this.titulo = "Nuevo título"; ❌ Error: 'titulo' es readonly
  }
}

```

🧩 También se puede usar con `static`, `private`, etc.
```ts
class Config {
  static readonly version = "1.0.0"; // Versión fija para toda la app
  private readonly claveSecreta: string;

  constructor(clave: string) {
    this.claveSecreta = clave;
  }

  showPrivateData() {
    console.log(this.claveSecreta);
  }
}

const baseConfig = new Config("cfg=5.0");
baseConfig.showPrivateData(); // ✅ Imprime: cfg=5.0


console.log(Config.version) // ver version de la app desde la clase
```

---

#### 📋 Gráfico

|**Modificador(es)**|**¿Pertenece a instancia o clase?**|**¿Accesible desde fuera?**|**¿Heredable?**|**¿Puede cambiarse su valor?**|**Uso común**|
|---|---|---|---|---|---|
|`public`|Instancia|✅ Sí|✅ Sí|✅ Sí|Atributos o métodos públicos del objeto|
|`private`|Instancia|❌ No|❌ No|✅ Sí|Encapsular lógica o datos internos|
|`protected`|Instancia|❌ No (solo subclases)|✅ Sí|✅ Sí|Herencia segura, acceso solo desde subclases|
|`static`|Clase|✅ (si `public`)|✅ (si `public`)|✅ Sí|Contador global, utilidad común, Singleton|
|`readonly`|Instancia (o clase si junto a static)|✅ (si `public`)|✅ Sí|❌ No|Constantes de solo lectura|
|`public static`|Clase|✅ Sí|✅ Sí|✅ Sí|Métodos o propiedades compartidas accesibles|
|`private static`|Clase|❌ No|❌ No|✅ Sí|Datos compartidos ocultos (ej. Singleton)|
|`protected static`|Clase|❌ No (solo subclases)|✅ Sí|✅ Sí|Comportamiento compartido entre clases hijas|
|`public readonly`|Instancia|✅ Sí|✅ Sí|❌ No|Constante visible desde fuera|
|`private readonly`|Instancia|❌ No|❌ No|❌ No|Constante interna, no modificable|
|`static readonly`|Clase|✅ Sí (si `public`)|✅ Sí|❌ No|Constante global de clase|