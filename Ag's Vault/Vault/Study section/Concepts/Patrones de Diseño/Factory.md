---
tags:
  - POO
  - medium
---
---

### 🏭  Que es?

-  Patrón de diseño de la programación orientada a objetos.
-  El patrón _Factory_ es una forma de organizar tu código para que, cuando necesites crear un objeto, en vez de crearlo directamente, le pides a una función o clase especial que te lo entregue ya preparado.

#### ✅ Cuando usar? Piénsalo de esta manera:

**"Tengo muchas opciones ya fijas, y si uso un factory para decidir cual devolver?**
Dado un tipo, quiero que me den el objeto correcto ya configurado, sin tener que saber cómo se construye internamente. Es como usar useReducer en react en lugar de useState por tener varias opciones.
Este es uno de los escenarios mas comunes, pero no solo se limita a esto, tambien puedes usarlo si no tienes opciones "Predefinidas" por ejemplo, si estas creando objetos a partir de valores dinámicos que te trae una api

**Por ejemplo, en vez de escribir tú mismo todas las instrucciones para crear un botón, solo llamas a una función que ya sabe cómo hacerlo. Tú solo le das una pista, como “quiero un botón rojo” o “quiero un botón con ícono”, y esa función se encarga de darte el botón correcto. Dame un ejemplo simple de esto**

Sin usar el patrón Factory:
```ts
// Crear un botón rojo manualmente
const botonRojo = {
  color: 'rojo',
  texto: 'Alerta',
  render: function () {
    console.log(`Botón rojo: ${this.texto}`);
  }
};

// Crear un botón con ícono manualmente
const botonIcono = {
  color: 'gris',
  icono: '🔔',
  render: function () {
    console.log(`Botón con ícono: ${this.icono}`);
  }
};

botonRojo.render();
botonIcono.render();
```

🏭 Con patrón Factory
```ts
// Suponemos que estas clases existen
class BotonIcono {
	render() {
		console.log("Renderizando botón con ícono");
	}
}

class BotonRojo {
	render() {
		console.log("Renderizando botón rojo");
	}
}

// El patrón Factory
function crearBoton(tipo: string) {
	switch (tipo) {
		case "icono":
			return new BotonIcono();
		case "rojo":
			return new BotonRojo();
		default:
			return null;
	}
}

const myBoton = crearBoton("icono");

if (myBoton) {
	myBoton.render();
} else {
	console.log("Tipo de botón no reconocido");
}

```

**no necesitas sí o sí crear clases previas** para aplicar el patrón Factory. Lo que necesitas es **una forma de generar objetos**, y eso puede ser con clases, funciones, objetos literales, o incluso funciones anónimas.
Por ejemplo podriamos tener una función factory que dado el switch case, simplemente retorne
un nuevo objeto:
```ts
return {
		src: "https://mylinkicon.com/icon.png",
		render() {
		console.log("Botón con ícono:", this.src);
	}
```

### ¿Cuándo usar clases?

- Si necesitas lógica más compleja.
- Si vas a tener muchos métodos o herencia.
- Si planeas expandir esa lógica en varios archivos o estructuras más grandes.
- 
Pero si solo estás generando estructuras simples con un par de propiedades o métodos, **funciones u objetos literales son más que suficiente**s

---

### ✅ Ventajas

- No necesitas repetir el mismo código cada vez que quieras crear algo similar.
- Si algún día cambias cómo se construyen esos botones, solo tienes que actualizar esa función, y todo lo demás sigue funcionando igual
En resumen, es una forma de que tu codigo sea mas facil de mantener y estructurado

#### ❌ cuándo no usarlo?

No necesitas un Factory si:
-  Solo tienes **un solo tipo de objeto** y no planeas tener variantes
-  No hay lógica compleja al cotruirlo (por ejemplo, solo hacer `new Boton()` y ya)
-  No te importa repetir un poco de código en distintos lugares (aunque eso suele ser una señal de que un Factory podría ayudarte a futuro)