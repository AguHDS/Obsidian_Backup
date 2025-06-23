---
tags:
  - Csharp
  - matriz
---

#### Que es?

A diferencia de los arrays y vectores unidimensionales, las **matrices** en C# son estructuras bidimensionales que se pueden imaginar como una **tabla** o **hoja de cálculo**, con **filas y columnas**.
por ejemplo si tenemos:

por ejemplo :
```csharp
int[,] matriz = {
    { 8, 23, 72 },
    { 10, 14, 16 }
};
```
La matriz tiene 2 filas y 3 columnas. Para acceder al número **14**, usamos índices de fila y columna:  
`matriz[1, 1]`
También existen **arrays de arrays** (`int[][]`), que no son matrices estrictas, pero se usan cuando las filas pueden tener diferentes longitudes.

Las matrices se declaran con una coma dentro de los corchetes. 

- `int[]` → array tradicional de una dimensión
- `int[,]` → array de dos dimensiones (como una tabla o matriz)
- `int[,,]` → array de tres dimensiones (como un cubo)
  
---

#### Cuando usar?

No son tan usados diariamente como los arrays comunes. Usas una matriz **cuando necesitas una estructura de datos rectangular fija**, como una **tabla** o **tablero**, donde cada fila tiene la misma cantidad de columnas.

- T ableros de juegos (ajedrez, sudoku, etc.)
-  Tablas matemáticas o matrices algebraicas
-  Representaciones de imágenes (en niveles básicos)
-  Lógicas de grillas o layouts en algoritmos