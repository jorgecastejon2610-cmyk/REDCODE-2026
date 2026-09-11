
```text
# 🏎️ WRO Futuros Ingenieros 2026 — Equipo REDCODE

```

---

> **Equipo REDCODE**
> 🏫 **Institución / Club:** Liceo Los Robles
> 🌍 **País:** Venezuela
> 👥 **Categoría:** Futuros Ingenieros (Future Engineers) — WRO 2026

---



*Figura 1: Demostración del vehículo autónomo LEGO del equipo REDCODE realizando la navegación y esquiva dinámica de obstáculos según la normativa oficial de la WRO 2026.*

---

## 📋 Tabla de Contenidos

* [Resumen Ejecutivo](#-resumen-ejecutivo)
* [Principios de Diseño e Ingeniería](#-principios-de-diseño-e-ingeniería)
* [Arquitectura de Hardware](#-arquitectura-de-hardware)
* [Arquitectura de Software y Lógica](#-arquitectura-de-software-y-lógica)
* [Estrategia de Navegación y Control PID](#-estrategia-de-navegación-y-control-pid)
* [Bitácora de Desarrollo e Iteraciones](#-bitácora-de-desarrollo-e-iteraciones)
* [Guía de Instalación y Carga del Código](#-guía-de-instalación-y-carga-del-código)
* [Estructura del Repositorio](#-estructura-del-repositorio)
* [Integrantes y Créditos](#-integrantes-y-créditos)

---

## 📌 Resumen Ejecutivo

El presente repositorio alberga la documentación de ingeniería, modelos de construcción LEGO, diagramas de conexión y código fuente del vehículo robótico autónomo desarrollado por el equipo **REDCODE** para la categoría **Futuros Ingenieros (WRO 2026)**.

Nuestra solución utiliza una placa controlador **BBC micro:bit** integrada en un chasis modular fabricado con elementos **LEGO**. El vehículo cuenta con un sistema de tracción y dirección basado en dos pares de ruedas (delanteras y traseras) y un arreglo tripartito de sensores de ultrasonido (frontal, izquierdo y derecho) que permiten la navegación precisa entre paredes y la evasión efectiva de obstáculos.

---

## ⚙️ Principios de Diseño e Ingeniería

Para maximizar el rendimiento y la consistencia en pista, REDCODE basó el desarrollo del robot en cuatro pilares fundamentales:

1. **Estructura Modular LEGO:** Construcción robusta y de fácil mantenimiento, optimizada para mantener una distribución equitativa del peso sobre los ejes delantero y trasero.
2. **Triangulación Ultrasónica:** Distribución estratégica de 3 sensores de ultrasonido para cobertura de 180° en el frente y laterales, eliminando puntos ciegos.
3. **Eficiencia en Microcontrolador:** Procesamiento directo en la placa micro:bit priorizando tiempos de respuesta inmediatos sin sobrecargar la memoria del procesador.
4. **Navegación Reactiva Suave:** Control proporcional para ajustar la velocidad y el ángulo de viraje en tiempo real según las lecturas diferenciales de distancia.

---

## 🛠️ Arquitectura de Hardware

### Lista de Materiales y Componentes (BOM)

| Categoría | Subsistema / Componente | Modelo / Especificación | Función y Justificación Técnica |
| --- | --- | --- | --- |
| **Controlador** | Microcontrolador | BBC micro:bit (v2) | Procesador principal, gestión de lógica y lectura de sensores. |
| **Placa de Expansión** | Driver / Módulo I/O | Módulo de expansión para micro:bit | Distribución de puertos GPIO y alimentación dedicada para servomotores y motores. |
| **Sensores** | Medición de Distancia | 3x Sensores de Ultrasonido (HC-SR04 / LEGO) | Cobertura tripartita: Sensor Izquierdo, Sensor Frontal y Sensor Derecho. |
| **Actuador Dirección** | Servomotor / Motor | Servo Micro / Motor LEGO Technic | Control del ángulo de dirección en el par de ruedas delanteras. |
| **Potencia Motriz** | Motor de Tracción | Motor DC / Motor LEGO Technic | Tracción del par de ruedas traseras para propulsión y reversa. |
| **Estructura** | Chasis y Transmisión | Componentes LEGO Technic | Chasis, engranajes, soportes de sensores y tren motriz. |
| **Ruedas** | Rodamiento | 2 Pares de Ruedas LEGO | Par delantero (dirección) y par trasero (tracción) de alto agarre. |
| **Energía** | Alimentación | Batería / Porta Baterías dedicado | Suministro continuo de voltaje regulado a la placa y actuadores. |



*Figura 2: Diagrama de conexiones eléctricas y sensores de ultrasonido con la micro:bit del equipo REDCODE.*

---

## 💻 Arquitectura de Software y Lógica

El programa se ejecuta dentro de un bucle de alta velocidad en la micro:bit encargándose del monitoreo sensorial constante.

```
                  [ Lectura de 3 Sensores de Ultrasonido ]
                                     │
            ┌────────────────────────┼────────────────────────┐
            ▼                        ▼                        ▼
 [Sensor Izquierdo (cm)]   [Sensor Frontal (cm)]    [Sensor Derecho (cm)]
            │                        │                        │
            └────────────────────────┼────────────────────────┘
                                     ▼
                      [ Máquina de Estados / Lógica ]
                                     │
               ┌─────────────────────┴─────────────────────┐
               ▼                                           ▼
 [Control PID de Dirección (Eje Frontal)]      [Ajuste de Velocidad (Eje Trasero)]
               │                                           │
               └─────────────────────┬─────────────────────┘
                                     ▼
                       [ Salida a Motores y Servos ]

```

---

## 🎯 Estrategia de Navegación y Control PID

El centrado del vehículo entre las paredes del circuito se logra evaluando la diferencia de lecturas entre el sensor izquierdo y el derecho:

$$\text{Error} = \text{Distancia Left} - \text{Distancia Right}$$

$$\text{Ángulo Dirección} = \text{Centro} + (K_p \cdot \text{Error}) + \left(K_d \cdot \frac{\Delta \text{Error}}{\Delta t}\right)$$

* **Sensor Frontal:** Actúa como gatillo de seguridad. Si detecta un obstáculo a menos de la distancia crítica, activa la rutina de evasión o viraje cerrado.
* **Sensores Laterales:** Mantienen el robot equilibrado en el centro del carril corregido suavemente por las ruedas delanteras.

---

## 📓 Bitácora de Desarrollo e Iteraciones

El desarrollo de REDCODE se dividió en fases documentadas en `/docs/bitacora-ingenieria.md`:

* **Fase 1 — Diseño del Chasis LEGO:** Ajuste del marco de componentes, alineación de las ruedas delanteras y traseras.
* **Fase 2 — Montaje Sensorial:** Calibración de la altura y posición de los 3 sensores ultrasónicos para evitar falsos rebotes con el suelo.
* **Fase 3 — Programación en micro:bit:** Implementación del código de lectura continua y filtrado de ruido en los sensores.
* **Fase 4 — Pruebas de Pista WRO 2026:** Ajuste de constantes del algoritmo de dirección y pruebas de recorrido continuo.

---

## 🚀 Guía de Instalación y Carga del Código

### Requisitos Previos

* **Entorno de Desarrollo:** MakeCode para micro:bit / Python para micro:bit
* **Conexión:** Cable USB a micro USB

### Pasos para Cargar el Programa

1. Abre el entorno de desarrollo de micro:bit.
2. Importa el archivo principal localizado en `/src/main.hex` (o `/src/main.py`).
3. Conecta tu placa **BBC micro:bit** al computador mediante el cable USB.
4. Descarga y transfiere el archivo directamente a la unidad de la micro:bit.

---

## 📂 Estructura del Repositorio

```text
.
├── README.md                   <-- Documentación principal
├── docs/                       <-- Documentación extendida
│   ├── bitacora-ingenieria.md  <-- Registro completo del proyecto
│   ├── arquitectura-sistema.png
│   └── media/                  <-- Fotografías y GIFs demostrativos
├── hardware/                   <-- Recursos de Ingeniería Física
│   ├── instrucciones-lego/     <-- Guías y fotos del armado del chasis
│   ├── esquematicos/           <-- Diagrama de puertos e interconexiones
│   └── lista-de-piezas.csv     <-- Desglose de piezas LEGO y electrónica
└── src/                        <-- Código Fuente del Robot
    ├── main.hex                <-- Archivo listo para flashear en micro:bit
    ├── main.py                 <-- Código fuente en Python para micro:bit
    └── controladores/          <-- Funciones para manejo de motores y ultrasonido

```

---

## 👥 Integrantes y Créditos

### Equipo REDCODE

* **Andrés Matos** — *Líder de Software y Programación micro:bit*
* **[Nombre Integrante 2]** — *Diseñador Mecánico y Ensamblador LEGO*
* **[Nombre Integrante 3]** — *Especialista en Electrónica y Calibración de Sensores*

### Entrenador / Mentor

* **[Nombre del Coach/Tutor]** — *Mentor Técnico y Asesor de Proyecto*

---

**Desarrollado por el Equipo REDCODE para la WRO 2026. 🚀**

```

```
