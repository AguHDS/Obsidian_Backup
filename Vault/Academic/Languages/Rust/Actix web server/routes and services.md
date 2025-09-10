---
tags:
  - rust
  - basic
---
---

#### Services

Registrar rutas usando servicios (`App::service`)
Se hace normalmente cuando defines **handlers o scopes** que luego quieres agregar como unidades completas.

Ejemplo con `service`:
```rust
use actix_web::{web, App, HttpServer, Responder, HttpResponse};

async fn hello() -> impl Responder {
    HttpResponse::Ok().body("Hola desde servicio")
}

async fn goodbye() -> impl Responder {
    HttpResponse::Ok().body("Adiós")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .service(
                web::scope("/api")      // Crea un scope
                    .route("/hello", web::get().to(hello))
                    .route("/goodbye", web::get().to(goodbye))
            )
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}
```

**resumen**
/api, es un servicio porque está dentro de service()
hello y goodbye tienen que ser llamados en la ruta /api/hello o /api/goodbye, ya que frman parte de ese servicio

---

### Routes

Se hace con métodos como `.route()` dentro de un `App`. Ejemplo:
```rust
use actix_web::{web, App, HttpServer, Responder, HttpResponse};

async fn hello() -> impl Responder {
    HttpResponse::Ok().body("Hola mundo")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .route("/", web::get().to(hello))  // RUTA MANUAL
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}
```

✅ **Ventajas**:

-  Sencillo y directo.
-  Ideal para pocas rutas o proyectos pequeños.
-  Control total sobre cada ruta.

❌ **Desventajas**:

-  Si tienes muchas rutas, tu `main.rs` se hace gigante y difícil de mantener.