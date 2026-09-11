# 🏎️ WRO Futuros Ingenieros 2026 — Equipo REDCODE

---

> **Equipo REDCODE**
> 🏫 **Institución:** Liceo Los Robles
> 🌍 **País:** Venezuela
> 👥 **Categoría:** Futuros Ingenieros — WRO 2026

---



*Figura 1: Demostración en pista del vehículo autónomo LEGO de REDCODE ejecutando la navegación reactiva y esquiva de obstáculos según la normativa WRO 2026.*

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

Este repositorio reúne toda la documentación técnica, esquemas de montaje LEGO, código fuente y pruebas en pista desarrolladas por el equipo **REDCODE** para competir en la categoría **Futuros Ingenieros WRO 2026**.

Nuestra propuesta se basa en un vehículo robótico autónomo impulsado por la tarjeta **BBC micro:bit** sobre una estructura modular de piezas **LEGO**. El sistema utiliza un tren de rodaje de cuatro ruedas distribuidas en dos pares, con las delanteras para la dirección y las traseras para la tracción, apoyado por un arreglo de tres sensores de ultrasonido para calcular distancias, centrarse en el carril y esquivar barreras en tiempo real.

---

## ⚙️ Principios de Diseño e Ingeniería

Para asegurar que el vehículo responda de manera constante y ágil durante la competencia, enfocamos el desarrollo en cuatro pilares:

1. **Estructura Modular LEGO:** Un chasis ligero y fácil de ajustar en boxes, optimizado para repartir el peso de forma equilibrada entre ambos ejes.
2. **Triangulación Ultrasónica:** Posicionamiento de tres sensores en abanico para cubrir un ángulo de 180° al frente y en los costados, eliminando puntos ciegos.
3. **Optimización en micro:bit:** Código directo y eficiente en la placa para procesar datos sensoriales al instante sin saturar el microcontrolador.
4. **Respuesta Proporcional:** Ajustes continuos de velocidad y ángulo de dirección basados en la diferencia de lectura entre paredes para lograr curvas fluidas.

---

## 🛠️ Arquitectura de Hardware

### Lista de Materiales y Componentes

| Categoría | Componente | Especificación Técnica | Función en el Vehículo |
| --- | --- | --- | --- |
| **Controlador** | Microcontrolador | BBC micro:bit v2 | Procesamiento de la lógica y gestión de puertos de entrada y salida |
| **Expansión** | Placa de Expansión | Expansion Shield para micro:bit | Interfaz física para sensores y señal hacia motores |
| **Sensores** | Medición de Distancia | 3 Sensores de Ultrasonido HC-SR04 | Cobertura en tres frentes: Sensor Izquierdo, Sensor Frontal y Sensor Derecho |
| **Dirección** | Actuador Servo | Micro Servomotor LEGO Technic | Control del ángulo de giro en el par de ruedas delanteras |
| **Tracción** | Motor Principal | Motor DC LEGO Technic | Impulso del par de ruedas traseras para el avance y retroceso |
| **Estructura** | Chasis | Piezas LEGO Technic | Conformación del chasis, soportes de sensores y tren motriz |
| **Ruedas** | Rodamiento | 2 Pares de Ruedas LEGO | Par delantero para dirección y par trasero para tracción de alto agarre |



*Figura 2: Esquema de conexiones de los sensores ultrasónicos y actuadores con la micro:bit.*

---

## 💻 Arquitectura de Software y Lógica

La micro:bit ejecuta un bucle de alta velocidad que lee constantemente los datos del entorno y toma decisiones sobre la tracción y el viraje:

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

## 🎯 Estrategia de Navegación y Control PID

Para mantener el robot centrado en el circuito, el programa calcula continuamente la diferencia entre el sensor izquierdo y el derecho:

```text
Error = Distancia_Izquierda - Distancia_Derecha
Angulo_Direccion = Centro + Kp * Error + Kd * Delta_Error

```

* **Sensor Frontal:** Sirve como barrera de seguridad. Cuando la distancia frontal cae por debajo del umbral de peligro, el vehículo reduce velocidad o inicia la maniobra de esquiva.
* **Sensores Laterales:** Guían las correcciones finas del eje delantero para mantener la trayectoria en el centro del carril.

---

## 📓 Bitácora de Desarrollo e Iteraciones

Documentamos todo el proceso de construcción, pruebas y fallos en el archivo `docs/bitacora-ingenieria.md`:

* **Fase 1 — Diseño del Chasis LEGO:** Construcción de la base física, alineación de las ruedas delanteras y ajuste del eje de tracción trasero.
* **Fase 2 — Montaje Sensorial:** Ubicación y ajuste de altura de los tres sensores de ultrasonido para evitar falsas lecturas con el suelo.
* **Fase 3 — Programación en micro:bit:** Creación del código de adquisición de datos y filtro de ruido para estabilizar las lecturas.
* **Fase 4 — Pruebas de Campo:** Ajuste de las constantes de dirección y pruebas de recorrido continuo en la pista de la WRO 2026.

---

## 🚀 Guía de Instalación y Carga del Código

### Entorno Sugerido

* **Plataforma:** MakeCode para micro:bit o editor Python micro:bit
* **Conexión:** Cable USB a micro USB

### Pasos para Cargar

1. Abre el entorno de programación para micro:bit.
2. Importa el archivo principal alojado en `src/main.hex` o `src/main.py`.
3. Conecta la tarjeta BBC micro:bit al computador mediante el cable USB.
4. Presiona la opción Descargar y transfiere el archivo compilado a la unidad de la tarjeta.

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

* **Andrés Matos** — Líder de Software y Programación micro:bit
* **Jorge Castejón** — Diseñador Mecánico y Ensamblador LEGO
* **Victor Boscan** — Especialista en Electrónica y Calibración de Sensores

### Mentor

* **Lic. Jose Escalona** — Mentor y Asesor de Proyecto

---

**Desarrollado por el Equipo REDCODE para la WRO 2026. 🚀**
