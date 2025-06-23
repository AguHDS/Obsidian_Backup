## 📁 My Files

### Funcionalidades principales

-  Permitir a los usuarios **subir archivos e imágenes**.
-  Esta opción solo estará disponible si el cliente **está logueado como usuario**.
    

### Rendimiento y carga

- Considerar el uso de **Web Workers** (por ejemplo, `dedicated-worker`) para manejar cargas pesadas al subir archivos.
    
    - Aunque puede que **no sea necesario** en este caso, ya que la subida es opcional y se haría en chunks.
    - Además, la interfaz mostrará un mensaje de “**subiendo archivos**”, bloqueando la interacción del usuario, por lo que **no es crítico** evitar el bloqueo del hilo principal.
    - **(Abierto a cambios)**

### Optimización

- **Comprimir archivos en el frontend** antes de enviarlos al servidor.

    - Esto es importante para reducir uso de almacenamiento y mejorar tiempos de subida.

### Configuraciones y visibilidad

- Opción para marcar cada sección como **pública o privada**.
    

### Visualización

- Implementar **scroll infinito (lazy loading)** cuando haya muchos archivos (imágenes, etc.), para ir cargando elementos de a poco y no saturar la memoria.
- Usar **D3.js** para mostrar una **gráfica de almacenamiento usado** en cada perfil de usuario.
    

### Seguridad

- **Reforzar la seguridad** para evitar accesos no autorizados cuando una sección esté marcada como privada.
    
    - Una vez finalizada esta sección, **intentar burlar su seguridad** para detectar vulnerabilidades.