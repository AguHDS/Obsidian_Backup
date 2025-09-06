crear note para esto
##### 🔹 ¿Qué son?

- **Chunks**: Porciones de datos grandes divididas en partes más pequeñas.  
    Ej: una imagen de 100MB se puede leer o enviar en trozos de 1MB.
    
- **Streams**: Flujos de datos que permiten procesar esos chunks de manera continua y eficiente, sin cargar todo en memoria.  
    Ej: leer un archivo línea por línea mientras lo procesas, o recibir un vídeo en vivo.
    

---

##### 🔸 En Node.js

- Es uno de los entornos **más conocidos por su uso intensivo de streams**, tanto para I/O de archivos como para peticiones HTTP.
    
- Usa módulos como `fs.createReadStream`, `http.IncomingMessage`, `stream.Transform`, etc.
    
- Muy útil porque Node es single-thread y necesita **eficiencia en manejo de memoria y eventos**.

### 🔸 En otros entornos también existen:

#### ✅ **Frontend (navegadores)**

- **Fetch API con Streams**:  
    Puedes hacer `fetch(url)` y usar `.body.getReader()` para leer chunks de la respuesta.

```ts
const response = await fetch('archivo-grande.mp4');
const reader = response.body.getReader();
```