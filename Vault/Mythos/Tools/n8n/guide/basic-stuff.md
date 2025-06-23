
#### Triggers
Los triggers son los Disparadores de nuestra secuencia lógica. Cuando se dé un evento determinado que elegimos (como un click), se dispara todo el flujo de trabajo.
### Actions
Las actiones se ejecutan ya sea dentro de nuestro entorno de trabajo o por fuera.

Ejemplo de actions por dentro de n8n
![[Pasted image 20250426200403.png]]

Ejemplo de actions por fuera de n8n. Son conexiones externas que nos ofrecen estos servicios
![[Pasted image 20250426200253.png]]

Las integraciones pueden ser tanto triggers como actions

# Nodos

### AI Transform
	
Programa un nodo usando lenguaje natural. Por ejemplo, escribes:  
_"Quítale el precio al string del precio y deja el dato en tipo número"_,  
y el nodo genera automáticamente el código Javascript necesario para realizar esa tarea.  
Este tipo de nodo convierte instrucciones en lenguaje natural en transformaciones de datos.

---
### Agentes de IA

Son bloques o nodos inteligentes que generan funciones únicas en función del contexto.  
Son más inteligentes que un chatbot tradicional porque **pueden actuar según el contexto**, utilizando lenguaje natural para recibir instrucciones.  
Se utilizan principalmente para **automatización de tareas**.

Existen tres elementos clave que definen un agente de IA:
- **Modelo:** El motor de IA que utilizamos (por ejemplo, OpenAI).
- **Memoria:** La capacidad de recordar interacciones o contextos previos.
- **Tools (Herramientas):** Servicios externos que el agente puede usar (por ejemplo, WhatsApp, API externas, bases de datos, etc).
    

### Agente Herramienta
- Usa **modelo, memoria y tools**.
- Excelente para interactuar con herramientas externas.
- Puede ejecutar acciones en servicios como WhatsApp, APIs, bases de datos, etc.
    

### Agente Conversacional
- Usa **modelo, memoria y tools**.
- Diseñado para mantener conversaciones contextuales.
- Puede recordar interacciones anteriores y responder de manera coherente en función del historial.
    

### Agente Planear y Ejecutar
- Usa **modelo y tools** (no memoria).
- Planifica y ejecuta acciones para cumplir un objetivo.
- No recuerda conversaciones previas, ya que su foco está en diseñar y ejecutar un plan en base a la solicitud actual.


# Función FromAI()

**Resumen corto:**
> **"$fromAI() sirve para extraer dinámicamente la respuesta generada por un nodo AI y usarla en otras partes del flujo de n8n. Es muy útil."**

La función `$fromAI()` en n8n se utiliza **dentro de un nodo AI** para **insertar dinámicamente respuestas generadas por la inteligencia artificial** en el flujo de trabajo.

**¿Qué hace exactamente?**  
Permite que dentro de una plantilla o de un campo dinámico puedas **tomar el resultado que el modelo de IA generó**.  
Así puedes usar esa respuesta como parte de otro procesamiento, en un mensaje, en una variable, o en un siguiente nodo.

**Ejemplo sencillo:**  
Supongamos que tienes un nodo de Agente Conversacional que genera esta respuesta:  
_"El precio final del producto es 45 dólares."_

En un campo de otro nodo (por ejemplo, un mensaje de WhatsApp o un correo), podrías usar:
```ts
El resultado fue: {{$fromAI("output")}}
```
y eso insertaría directamente **"El precio final del producto es 45 dólares."** en ese mensaje.

**Forma general:**
```ts
{{$fromAI("nombreDelCampo")}}
```
- `"output"` es el valor más común, pero depende de qué definiste como salida en tu nodo AI.
-  Si configuraste el agente para generar varios campos, puedes usar `$fromAI("nombreCampoEspecífico")`
