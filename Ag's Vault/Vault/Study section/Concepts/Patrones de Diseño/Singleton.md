---
tags:
  - POO
  - basic
---

Patrón de diseño de la programación orientada a objetos
El patrón de diseño Singleton consiste en crear una clase única en todo el sistema y que tenga acceso global

### ¿Cómo funciona?

1. La clase crea su única instancia, almacenándola en una variable estática
2. Oculta su constructor para que nadie más pueda crear objetos directamente
3. Expone un método como `getInstance()` que devuelve esa única instancia

### ¿Cuándo se usa?
Úsalo cuando:

- Solo tiene sentido tener una única instancia de algo
- Necesitas **coordinación global** (por ejemplo, que todos los módulos usen el mismo logger)
- Quieras evitar crear múltiples instancias costosas o con estados compartidos
### ¿Cuándo **no** usarlo?

- En aplicaciones grandes, **abusar del Singleton puede generar dependencias ocultas y dificultad para testear**.
- Se considera un **antipatrón** en algunos contextos por romper el principio de inversión de dependencias y hacer que el código dependa de "cosas globales".