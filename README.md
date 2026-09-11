# 🏎️ WRO Future Engineers 2026 — Team REDCODE

<p align="center">
  <img src="https://img.shields.io/badge/WRO-Futuros_Ingenieros_2026-blue?style=for-the-badge&logo=robotics" alt="WRO Category" />
  <img src="https://img.shields.io/badge/Licencia-MIT-green?style=for-the-badge&logo=open-source-initiative" alt="License" />
  <img src="https://img.shields.io/badge/Estado-Optimizado-brightgreen?style=for-the-badge&logo=check-circle" alt="Build Status" />
  <img src="https://img.shields.io/badge/Python-3.9+-yellow?style=for-the-badge&logo=python" alt="Python Version" />
</p>

---

> **Equipo REDCODE**  
> 🏫 **Institución / Club:** Liceo Los Robles  
> 🌍 **País:** Venezuela  
> 👥 **Categoría:** Future Engineers (Futuros Ingenieros) — WRO 2026  

---

<p align="center">
  <img src="docs/media/demostracion-pista.gif" alt="Demostración en Pista" width="85%" />
  <br>
  <em>Figura 1: Demostración del vehículo autónomo del equipo REDCODE ejecutando el recorrido completo sin colisiones y realizando esquiva dinámica de obstáculos según la normativa oficial WRO 2026.</em>
</p>

---

## 📋 Tabla de Contenidos
- [Resumen Ejecutivo](#-resumen-ejecutivo)
- [Principios de Diseño e Ingeniería](#-principios-de-diseño-e-ingeniería)
- [Arquitectura de Hardware](#-arquitectura-de-hardware)
- [Arquitectura de Software y Visión](#-arquitectura-de-software-y-visión)
- [Estrategia de Control PID y Navegación](#-estrategia-de-control-pid-y-navegación)
- [Bitácora de Desarrollo e Iteraciones](#-bitácora-de-desarrollo-e-iteraciones)
- [Guía de Instalación y Despliegue](#-guía-de-instalación-y-despliegue)
- [Estructura del Repositorio](#-estructura-del-repositorio)
- [Integrantes y Créditos](#-integrantes-y-créditos)

---

## 📌 Resumen Ejecutivo

El presente repositorio alberga la documentación de ingeniería, modelos CAD, diagramas esquemáticos y la suite de software del vehículo robótico autónomo desarrollado por el equipo **REDCODE** para la categoría **Futuros Ingenieros (WRO 2026)**.

Nuestra propuesta se enfoca en la implementación de una arquitectura modular basada en procesamiento de visión por computadora en tiempo real, combinada con un chasis de bajo centro de masa con geometría de dirección **Ackermann**. La plataforma procesa señales visuales a alta velocidad para resolver simultáneamente la navegación de carril, la clasificación de obstáculos por código de color (rojo/verde) y la toma de decisiones en curvas cerradas mediante una máquina de estados finitos (FSM).

---

## ⚙️ Principios de Diseño e Ingeniería

Para maximizar el rendimiento y la confiabilidad en pista, REDCODE basó el desarrollo del robot en cuatro pilares fundamentales:

1. **Estabilidad Cinemática:** Geometría Ackermann pura con centro de rotación alineado al eje trasero, minimizando el deslizamiento transversal en virajes de radio reducido.
2. **Modularidad Hardware/Software:** Desacoplamiento total entre la capa de percepción visual, la lógica de decisión estratégica y la capa de control de actuadores.
3. **Distribución Térmica y Energética:** Aislamiento eléctrico entre las líneas de potencia motoras y los buses lógicos, integrado con disipación pasiva para extensas sesiones de pruebas.
4. **Resiliencia Sensorial:** Implementación de máscaras de espacio de color HSV dinámicas y filtros digitales (promedio móvil / Kalman) para compensar variaciones de iluminación ambiental en pista.

---

## 🛠️ Arquitectura de Hardware

### Lista de Materiales y Componentes (BOM)

| Categoría | Subsistema / Componente | Modelo / Especificación | Función y Justificación Técnica |
| :--- | :--- | :--- | :--- |
| **SBC (Computadora)** | Procesador Principal | Raspberry Pi 4 B (4GB) / Jetson Nano | Procesamiento de fotogramas OpenCV a +30 FPS y ejecución de lógica principal. |
| **Visión Artificial** | Sensor Optoelectrónico | Raspberry Pi Camera Module v2 | Lente de amplio campo visual para detección temprana de balizas y bordes. |
| **Control de Dirección**| Actuador de Servo | Servomotor MG996R (Torque 10 kg/cm) | Mecanismo de alta precisión para el control del ángulo de ataque frontal. |
| **Potencia Motriz** | Motor de Tracción | Motor DC Cepillado + Reductora metal | Proporción torque-velocidad optimizada para aceleraciones suaves. |
| **Driver de Motores** | Etapa de Potencia H-Bridge | Cytron MD10C / L298N Modificado | Control PWM de alta frecuencia para eliminar ruido térmico y vibración. |
| **Energía** | Banco de Baterías | Batería LiPo 3S 11.1V (2200 mAh) | Alimentación central con alta tasa de descarga (C-rating). |
| **Regulación** | Convertidores DC-DC | Regulador Buck LM2596 (5V/3A) | Líneas independientes de voltaje estable para la SBC y los servomotores. |

<p align="center">
  <img src="docs/arquitectura-sistema.png" alt="Arquitectura de Hardware" width="75%" />
  <br>
  <em>Figura 2: Diagrama de distribución eléctrica e interconexión de buses (I2C, PWM, GPIO) del vehículo REDCODE.</em>
</p>

---

## 💻 Arquitectura de Software y Visión

El pipeline de procesamiento se ejecuta de forma asíncrona mediante multiprocesamiento para prevenir latencias en el ciclo de control.

```
                    [ Captura de Cuadro (Pi Camera) ]
                                    │
                                    ▼
                     [ Conversión a Espacio HSV ]
                                    │
            ┌───────────────────────┴───────────────────────┐
            ▼                                               ▼
 [Máscaras de Color Paredes]                     [Máscaras Bloques R/V]
            │                                               │
            ▼                                               ▼
[Cálculo de Punto Medio (ROI)]                   [Filtrado de Contornos/Área]
            │                                               │
            └───────────────────────┬───────────────────────┘
                                    ▼
                    [ Máquina de Estados Finitos ]
                                    │
                                    ▼
                    [ Bucle de Control PID Dirección ]
                                    │
                                    ▼
                    [ Salida PWM a Motores y Servo ]
```

### 1. Procesamiento de Imagen (OpenCV)
* **Región de Interés (ROI):** Filtrado espacial de la mitad inferior de la imagen para eliminar interferencias del público o fondo.
* **Segmentación HSV:** Umbralización adaptativa para aislar tonos de color bajo diferentes condiciones de luz.
* **Filtros Morfológicos:** Operaciones de Erosión y Dilatación (Kernel 3x3) para eliminar ruido fotónico y pequeños reflejos.

---

## 🎯 Estrategia de Control PID y Navegación

El control de la dirección del vehículo se rige mediante un algoritmo Proporcional-Integrativo-Derivativo (PID) aplicado al error de alineación central e(t):

e(t) = X_objetivo - X_medido

Ángulo Servo = Kp * e(t) + Ki * ∫e(τ)dτ + Kd * (de(t)/dt)

* **Término Proporcional (Kp):** Aporta la respuesta inmediata en función de la magnitud del desvío del carril.
* **Término Integrativo (Ki):** Corrección de sesgos sistemáticos en rectas largas (mantenido cercano a cero para evitar oscilaciones).
* **Término Derivativo (Kd):** Amortigua la sobreoscilación al aproximarse rápidamente al centro o entrar a una curva cerrada.

---

## 📓 Bitácora de Desarrollo e Iteraciones

El desarrollo de REDCODE se estructuró en cuatro fases iterativas documentadas minuciosamente en `/docs/bitacora-ingenieria.md`:

* **Semana 1–3 — Diseño y Prototipado Base:** Elección de la geometría Ackermann frente a dirección diferencial. Impresión 3D del chasis v1.
* **Semana 4–6 — Integración Electrónica y Calibración:** Instalación de la etapa de potencia, pruebas de consumo térmico y aislamiento de ruido eléctrico con condensadores de desacoplamiento.
* **Semana 7–9 — Desarrollo del Algoritmo de Visión:** Pruebas del filtro HSV e implementación del hilo secundario de cámara para garantizar +30 FPS.
* **Semana 10+ — Optimización PID y Evasión:** Calibración fina de constantes Kp, Kd, pruebas intensivas de esquiva de bloques y optimización de velocidad en curva.

---

## 🚀 Guía de Instalación y Despliegue

### Requisitos del Sistema
* **SO Sugerido:** Raspberry Pi OS / Ubuntu Server (64-bit)
* **Python:** v3.9 o superior

### 1. Clonar el Repositorio
```bash
git clone https://github.com/TuUsuario/WRO-2026-REDCODE.git
cd WRO-2026-REDCODE
```

### 2. Entorno Virtual e Instalación de Dependencias
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 3. Modos de Ejecución
```bash
# Modo Calibración de Cámara (Ajuste de Máscaras HSV en Tiempo Real)
python3 src/main.py --mode calibrate

# Modo Carrera Autónomo (Competición Oficial WRO 2026)
python3 src/main.py --mode autonomous
```

---

## 📂 Estructura del Repositorio

```text
.
├── README.md                   <-- Documentación principal
├── LICENSE                     <-- Licencia de Código Abierto (MIT)
├── requirements.txt            <-- Lista de librerías Python necesarias
├── docs/                       <-- Documentación extendida
│   ├── bitacora-ingenieria.md  <-- Registro completo de desarrollo
│   ├── arquitectura-sistema.png
│   └── media/                  <-- Imágenes y GIFs Demostrativos
├── hardware/                   <-- Recursos de Ingeniería Física
│   ├── cad/
│   │   ├── fuente/             <-- Archivos editables (Fusion 360 / SolidWorks)
│   │   └── stl/                <-- Modelos listos para impresión 3D
│   ├── esquematicos/           <-- Diagramas de circuito y esquemas PCB
│   └── bill-of-materials.csv   <-- Desglose de piezas y presupuesto
└── src/                        <-- Código Fuente del Robot
    ├── main.py                 <-- Punto de entrada y máquina de estados
    ├── vision/                 <-- Módulos OpenCV y segmentación
    ├── control/                <-- Controlador PID y manejo de PWM
    └── utils/                  <-- Controladores de sensores y telemetría
```

---

## 👥 Integrantes y Créditos

### Equipo REDCODE
* **Andrés Matos** — *Software Architect & Computer Vision Lead*
* **[Nombre Integrante 2]** — *Hardware Engineer & CAD Mechanical Designer*
* **[Nombre Integrante 3]** — *Embedded Systems & Electronics Specialist*

### Entrenador / Mentor
* **[Nombre del Coach/Tutor]** — *Mentor Técnico y Asesor de Proyecto*

---

<p align="center">
  <b>Desarrollado con pasión y precisión por el Equipo REDCODE para la WRO 2026. 🚀</b>
</p>
