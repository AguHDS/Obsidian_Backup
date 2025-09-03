---
tags:
  - general
---
---
## **Programación Embebida**

### 📝 Qué es

La **programación embebida** es el desarrollo de **software (firmware)** que corre directamente en **dispositivos electrónicos con recursos limitados** (microcontroladores, placas, chips).
Por ejemplo, en la placa madre, la bios (firmware) es el codigo que esta grabado en un chip que hace funcionar esta, hecho con programación embebida.

### 🔹 Características

-  Corre en **hardware dedicado**.
-  Uso de lenguajes de **bajo nivel**: C, C++, Rust.
-  Control directo de **memoria y hardware**.
-  Se busca **eficiencia y estabilidad** más que flexibilidad.

### 🔹 Ejemplos

-  Firmware de un control remoto.
-  Software en un sensor de temperatura.
-  Sistema que hace funcionar un microondas.

### 💡 Idea clave

👉 **Embebido = cerebro del dispositivo.**  
Puede funcionar solo, sin internet ni nube.

---

#### Cuando es firmware y cuando es software embebido?

Todo firmware es software embebido, pero no todo software embebido se llama firmware.

## 🔹 Diferencia

-  **Firmware**
    
    -  Es el software embebido **que está grabado en la memoria del dispositivo** (ROM, flash, EEPROM).
    -  Normalmente es **esencial** para que el hardware arranque y funcione.
    -  Ejemplo: el BIOS/UEFI de la placa madre, el sistema de un router, el código de un microcontrolador.

- **Software embebido (general)**
    
    -  Puede incluir firmware, pero también programas adicionales que corren **encima del firmware** en un dispositivo embebido.
        
    -  Ejemplo:
        
        - En un Smart TV → el **firmware** inicializa la tele, pero luego corre un **sistema operativo embebido** (Linux recortado) y aplicaciones (Netflix, YouTube).
        
        - En un auto → hay firmware en cada microcontrolador, pero también software embebido más complejo que gestiona la inyección de combustible, el ABS, etc.
        

---

## 🔹 Relación

📌 Se puede ver así:

-  **Firmware** = “capa básica” de software embebido, grabada en el dispositivo.
-  **Software embebido** = cualquier programa diseñado para correr en hardware específico (incluye firmware y capas superiores).