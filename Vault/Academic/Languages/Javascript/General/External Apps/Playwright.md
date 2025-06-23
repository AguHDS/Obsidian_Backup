---
tags:
  - javascript
---

---

#### Qué es Playwright?

Playwright es una herramienta de automatización de navegadores. Se usa para:

-  Automatizar interacciones con páginas web (hacer clics, rellenar formularios).
-  Hacer scraping de páginas dinámicas.   
-  Probar interfaces web en navegadores reales.

**Pero**:  Playwright no se usa dentro de extensiones de navegador.

Sirve para simular la interacción de un usuario real con una aplicación web, como hacer clics, llenar formularios, navegar entre páginas, etc.

#### Ejemplo
puedes escribir una prueba como esta:

```ts
import { test, expect } from '@playwright/test';

test('formulario de login', async ({ page }) => {
  await page.goto('https://mi-app.com/login');
  await page.fill('#email', 'usuario@ejemplo.com');
  await page.fill('#password', '123456');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('https://mi-app.com/dashboard');
});
```

### 🤖 **Automatización general de navegadores**

También se puede usar para:

-  Scraping de sitios web (como lo harías con Puppeteer o Selenium).
-  Tomar capturas de pantalla o generar PDFs.
-  Automatizar flujos manuales como login en sitios de terceros.

### 🎯 ¿Por qué elegir Playwright sobre otras herramientas?

-  **Más rápido y confiable que Selenium.**
-  **Soporte para múltiples pestañas y contextos** (simular distintos usuarios en paralelo).
-  **Detección automática de elementos inestables (flaky tests)**.
-  **Herramientas integradas de grabación** para generar código de prueba automáticamente.
    

---

### 💼 ¿Quién lo usa?

Playwright es usado por desarrolladores frontend y QA engineers en empresas que quieren asegurarse de que su frontend funciona correctamente en distintos navegadores, sin tener que probar manualmente.