---
tags:
  - basic
  - javascript
---
---

#### Que es
Es el comportamiento donde las **declaraciones de variables y funciones se “mueven” al inicio del ámbito de ejecución** durante la fase de compilación.

-  **Declaraciones `var`**: la variable se declara al inicio, pero su **valor inicial es `undefined`**.
-  **Declaraciones `let` y `const`**: también se “levantan”, pero están en un **temporal dead zone** hasta que se asigna el valor.
-  **Funciones declaradas con `function miFn() {}`**: se elevan **completamente**, incluyendo su implementación, por lo que puedes llamarlas antes de su definición en el código.


Ejemplo

1.  Qué mostrará por consola el siguiente código?  
```js
console.log(miFn());  
var miFn = () => console.log("Hola");  
```
Respuesta: Error. Te va a decir que miFn is not a function porque debido al hoisting y a que la variable es tipo var, su valor inicial (previo a la ejecución) por defecto es undefined por ende undefined no se puede ejecutar como una función, undefined() no existe.

2.  Y en este caso?
```js
console.log(miFn());  
const miFn = () => console.log("Hola");
```
Respuesta: Error. Va a devolver que no puede acceder a miFn antes de la inicialización porque, al ser const, el hoisting sucede pero la variable queda en la zona muerta temporal por ende existe pero es inaccesible hasta llegar a la línea donde se ejecuta (osea que desde esa línea hacia abajo no hay drama pero hacia arriba todavía no se inicializó).

