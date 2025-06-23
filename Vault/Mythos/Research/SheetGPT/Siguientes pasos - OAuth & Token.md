Ya hice el proyecto en Google Cloud Console, para mostrar un modal de consentimiento del usuario para que nos de acceso a los recursos que necesitamos para hacer lo que queremos. Tambien ya tengo mi Client ID, establecí los scopes, ya tengo codigo en background.js para obtener un token de sesion del usuario.

Mi objetivo es crear e inyectar automáticamente funciones personalizadas en el entorno de Apps Script del usuario, para que luego este pueda pararse en una celda y hacer uso de las funciones que le dí, como =GPT_LIST("dame una lista de 5 animales")
Ya tengo creada la UI, el backend (aunque por ahora solo tiene codigo para consumir la api de OpenAI).

Este es mi roadmap actual:
### ✅ Paso 1 – Crear el proyecto Apps Script vía API (vinculado a la cuenta del usuario)

#### Objetivo:
Crear un proyecto de Apps Script en la cuenta Drive del usuario con el título que tú quieras.

#### Endpoint:
`POST https://script.googleapis.com/v1/projects`

#### Cuerpo JSON:
```json
{
  "title": "Excellent GPT Functions"
}

```
 Resultado:
Te devolverá un `scriptId`, que usarás en el próximo paso.


### ✅ Paso 2 – Subir tus funciones (código) a ese proyecto

#### Objetivo:

Cargar tu(s) función(es) App Script como archivos `.gs` (SERVER_JS) y el manifest `appsscript.json`.

#### Endpoint:
`PUT https://script.googleapis.com/v1/projects/{scriptId}/content`

#### Cuerpo JSON:
```json
{
  "files": [
    {
      "name": "GPTFunctions",
      "type": "SERVER_JS",
      "source": "function GPT_LIST(...) { ... }"
    },
    {
      "name": "appsscript",
      "type": "JSON",
      "source": JSON.stringify({
        "timeZone": "Etc/GMT",
        "exceptionLogging": "STACKDRIVER",
        "runtimeVersion": "V8"
      })
    }
  ]
}
```

### ✅ Paso 3 – Asociar ese script al archivo de Google Sheets actual (opcional, pero recomendable)

Este paso es necesario si quieres que el script funcione directamente como **función personalizada en celdas (`=GPT_LIST(...)`)**.

#### Opciones:

- Crear un script **container-bound** (asociado al documento).
    
- O simplemente instruir al usuario que debe ir a "Extensiones > Apps Script" y verás que el script ya está ahí.
    

> La API de Apps Script **no permite crear directamente container-bound scripts**, pero puedes copiar uno y vincularlo usando Drive API. Si no, dejarlo como "standalone" funciona igualmente, aunque requiere que el usuario autorice una vez.

---

### ✅ Paso 4 – Instruir o automatizar el despliegue (facultativo)

Por ahora puedes dejar el script cargado, y se usará como función personalizada si:

- El script tiene permisos suficientes.
    
- El archivo ya tiene abierto el documento.
    

También puedes desplegarlo como "ejecutable" o Apps Script Web App si necesitas invocarlo desde otros contextos.

---

### ✅ Paso 5 – Validar permisos en tiempo real

- Puedes usar el token para hacer una llamada a `https://www.googleapis.com/oauth2/v3/userinfo` y obtener el correo del usuario si necesitas asociarlo.
    
- Puedes validar que el script está creado correctamente con una llamada a `GET https://script.googleapis.com/v1/projects/{scriptId}`
    

---

## 📦 Estructura del código a implementar ahora

### En tu frontend (sidebar o fondo):

1. `getOAuthToken()` ✅
    
2. `crearProyecto(token)`
    
3. `subirCodigo(token, scriptId, codigoString)`
    
4. `mostrarConfirmación("Script instalado")`