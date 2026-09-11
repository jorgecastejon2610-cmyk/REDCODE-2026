# WRO Futuros Ingenieros 2026 — Equipo REDCODE

---

> **Equipo REDCODE**
> **Institución:** Liceo Los Robles
> **País:** Venezuela
> **Categoría:** Futuros Ingenieros — WRO 2026

---



*Figura 1: Demostración en pista del vehículo autónomo LEGO de REDCODE ejecutando la navegación reactiva y esquiva de obstáculos según la normativa WRO 2026.*

---

## Tabla de Contenidos

* [Resumen Ejecutivo](#resumen-ejecutivo)
* [Principios de Diseño e Ingeniería](#principios-de-diseño-e-ingeniería)
* [Arquitectura de Hardware](#arquitectura-de-hardware)
* [Arquitectura de Software y Lógica](#arquitectura-de-software-y-lógica)
* [Estrategia de Navegación y Control PID](#estrategia-de-navegación-y-control-pid)
* [Bitácora de Desarrollo e Iteraciones](#bitácora-de-desarrollo-e-iteraciones)
* [Guía de Instalación y Carga del Código](#guía-de-instalación-y-carga-del-código)
* [Estructura del Repositorio](#estructura-del-repositorio)
* [Integrantes y Créditos](#integrantes-y-créditos)

---

## Resumen Ejecutivo

Este repositorio reúne la documentación técnica, esquemas de montaje LEGO, código fuente y pruebas en pista desarrolladas por el equipo **REDCODE** para competir en la categoría **Futuros Ingenieros WRO 2026**.

El sistema consiste en un vehículo robótico autónomo impulsado por la tarjeta **BBC micro:bit** sobre un chasis modular de piezas **LEGO**. La plataforma cuenta con un tren de rodaje de cuatro ruedas distribuidas en dos pares, utilizando las delanteras para la dirección y las traseras para la tracción. El control ambiental se apoya en un arreglo de tres sensores de ultrasonido para medir distancias, calcular el centro del carril y esquivar barreras en tiempo real.

---

## Principios de Diseño e Ingeniería

El desarrollo del vehículo se estructuró sobre cuatro pilares técnicos fundamentales:

1. **Estructura Modular LEGO:** Chasis ligero y configurable en taller, diseñado para distribuir el peso de forma equilibrada entre ambos ejes.
2. **Triangulación Ultrasónica:** Distribución de tres sensores en abanico para cubrir 180° frontales y laterales, eliminando puntos ciegos de detección.
3. **Optimización en micro:bit:** Implementación de código de baja latencia para procesar lecturas sensoriales sin saturar el microcontrolador.
4. **Respuesta Proporcional:** Ajuste continuo de velocidad y ángulo de dirección según el diferencial de distancia con las paredes para trazados fluidos.

---

## Arquitectura de Hardware

### Componentes del Sistema

* **Microcontrolador:** BBC micro:bit v2 para el procesamiento de la lógica principal y la gestión de puertos de entrada y salida.
* **Placa de Expansión:** Expansion Shield para micro:bit como interfaz física de sensores y transmisión de señal hacia los motores.
* **Sensores de Distancia:** 3 sensores de ultrasonido HC-SR04 distribuidos en posición izquierda, frontal y derecha.
* **Actuador de Dirección:** Micro servomotor LEGO Technic para la variación del ángulo de giro en el par de ruedas delanteras.
* **Motor de Tracción:** Motor DC LEGO Technic para la propulsión y retroceso del par de ruedas traseras.
* **Estructura Física:** Piezas LEGO Technic para la conformación del chasis, soportes sensoriales y tren motriz.
* **Sistema de Rodaje:** 2 pares de ruedas LEGO, utilizando el par delantero para la guía de dirección y el par trasero para la tracción.



*Figura 2: Esquema de conexiones de los sensores ultrasónicos y actuadores con la micro:bit.*

---

## Arquitectura de Software y Lógica

La micro:bit ejecuta un bucle de alta frecuencia que procesa las entradas del entorno para determinar las salidas de tracción y viraje:

```text
                  [ Lectura de 3 Sensores de Ultrasonido ]
                                     │
            ┌────────────────────────┼────────────────────────┐
            ▼                        ▼                        ▼
 [Sensor Izquierdo cm]     [Sensor Frontal cm]      [Sensor Derecho cm]
            │                        │                        │
            └────────────────────────┼────────────────────────┘
                                     ▼
                      [ Máquina de Estados / Lógica ]
                                     │
               ┌─────────────────────┴─────────────────────┐
               ▼                                           ▼
 [Control PID de Dirección Eje Frontal]        [Ajuste de Velocidad Eje Trasero]
               │                                           │
               └─────────────────────┬─────────────────────┘
                                     ▼
                       [ Salida a Motores y Servos ]

```

---

## Estrategia de Navegación y Control PID

Para mantener la posición centrada en la pista, el algoritmo evalúa de forma continua el diferencial entre los sensores laterales:

```text
Error = Distancia_Izquierda - Distancia_Derecha
Angulo_Direccion = Centro + Kp * Error + Kd * Delta_Error

```

* **Sensor Frontal:** Umbral de seguridad para reducir la velocidad o activar maniobras de evitación cuando la distancia frontal cae por debajo del límite crítico.
* **Sensores Laterales:** Proveen los datos de corrección fina al eje delantero para ajustar la trayectoria sobre la línea central del carril.

---

## Bitácora de Desarrollo e Iteraciones

El proceso de diseño, calibración y correcciones se encuentra documentado en `docs/bitacora-ingenieria.md`:

* **Fase 1 — Diseño del Chasis LEGO:** Ensamble de la estructura base, alineación del tren delantero de dirección y acople de la transmisión trasera.
* **Fase 2 — Montaje Sensorial:** Fijación y ajuste de elevación de los tres sensores ultrasónicos para prevenir reflexiones erróneas con la pista.
* **Fase 3 — Programación en micro:bit:** Desarrollo de los algoritmos de adquisición de señal y filtros de datos para mitigar el ruido sensorial.
* **Fase 4 — Pruebas de Campo:** Ajuste fino de las constantes de control y validación de autonomía en la pista oficial WRO 2026.

---

## Guía de Instalación y Carga del Código

### Entorno Sugerido

* **Plataforma de Desarrollo:** MakeCode para micro:bit o editor Python para micro:bit
* **Conexión de Hardware:** Cable USB a micro USB

### Pasos para Cargar

1. Abrir la interfaz de programación para micro:bit.
2. Importar el archivo alojado en `src/main.hex` o `src/main.py`.
3. Conectar la tarjeta BBC micro:bit al equipo mediante el cable USB.
4. Seleccionar la opción de descarga y transferir el binario compilado a la unidad del dispositivo.

---

## Estructura del Repositorio

```text
.
├── README.md                   <-- Documentación principal
├── docs/                       <-- Documentación extendida
│   ├── bitacora-ingenieria.md  <-- Registro completo del proyecto
│   ├── arquitectura-sistema.png
│   └── media/                  <-- Fotografías y GIFs demostrativos
├── hardware/                   <-- Recursos de Ingeniería Física
│   ├── instrucciones-lego/     <-- Guías y fotos del armado del chasis
│   └── esquematicos/           <-- Diagrama de puertos e interconexiones
└── src/                        <-- Código Fuente del Robot
    ├── main.hex                <-- Archivo listo para flashear en micro:bit
    ├── main.py                 <-- Código fuente en Python para micro:bit
    └── controladores/          <-- Funciones para manejo de motores y ultrasonido

```

---

## Integrantes y Créditos

### Equipo REDCODE

* **Andrés Matos** — Líder de Software y Programación micro:bit
* **Jorge Castejón** — Diseñador Mecánico y Ensamblador LEGO
* **Victor Boscan** — Especialista en Electrónica y Calibración de Sensores

### Mentor

* **Lic. Jose Escalona** — Mentor y Asesor de Proyecto

---

**Desarrollado por el Equipo REDCODE para la WRO 2026.**
