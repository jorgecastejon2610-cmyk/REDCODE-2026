<img width="500" height="500" alt="image-removebg-preview" src="https://github.com/user-attachments/assets/2a019ebf-dbdc-4fd9-bb43-110b1ea4b76e" />


# WRO Futuros Ingenieros 2026 — Equipo REDCODE

---

> **Equipo REDCODE**
> **Institución** Liceo Los Robles
> **País** Venezuela
> **Categoría** Futuros Ingenieros — WRO 2026

---



*Figura 1. Demostración en pista del vehículo autónomo LEGO de REDCODE ejecutando la navegación reactiva y esquiva de obstáculos según la normativa WRO 2026.*

<img width="960" height="1280" alt="foto 1" src="https://github.com/user-attachments/assets/270e4f0f-9379-4c75-b120-6f441b3ba24b" />


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

Este repositorio reúne la documentación técnica, esquemas de montaje LEGO, código fuente y pruebas en pista desarrolladas por el equipo REDCODE para competir en la categoría Futuros Ingenieros WRO 2026.

El sistema consiste en un vehículo robótico autónomo impulsado por la tarjeta BBC micro:bit sobre un chasis modular de piezas LEGO. Todo el programa fue estructurado, codificado y optimizado en Python utilizando la plataforma Zitronik. La plataforma cuenta con un tren de rodaje de cuatro ruedas distribuidas en dos pares, utilizando las delanteras para la dirección y las traseras para la tracción. El control ambiental se apoya en un arreglo de tres sensores de ultrasonido para medir distancias, calcular el centro del carril y esquivar barreras en tiempo real.

---

## Principios de Diseño e Ingeniería

El desarrollo del vehículo priorizó la construcción de una estructura modular LEGO liviana pero rígida, pensada para lograr un centro de gravedad equilibrado entre ambos ejes y permitir ajustes mecánicos rápidos directamente en los boxes de competencia.

Para la percepción del entorno implementamos un arreglo en abanico de tres sensores de ultrasonido, posicionados tácticamente para cubrir un campo visual de 180° que elimina puntos ciegos y permite medir paredes laterales y obstáculos frontales de forma simultánea.

En el apartado lógico, todo el código fue programado en Python desde la plataforma Zitronik, optimizando los ciclos de procesamiento en la tarjeta micro:bit para ejecutar lecturas en tiempo real y aplicar correcciones proporcionales de velocidad y dirección sin generar latencia.

---

## Arquitectura de Hardware

### Componentes del Sistema

* Microcontrolador BBC micro:bit v2 para el procesamiento de la lógica principal y gestión de entradas y salidas.
* Placa de expansión Expansion Shield para micro:bit como interfaz física de conexión.
* 3 sensores de ultrasonido HC-SR04 ubicados en el frente, izquierda y derecha.
* Micro servomotor LEGO Technic para el control del ángulo de giro en el eje delantero.
* Motor DC LEGO Technic para la tracción y retroceso del eje trasero.
* Chasis, soportes y tren motriz construidos íntegramente con piezas LEGO Technic.
* 2 pares de ruedas LEGO, el par delantero para guía de dirección y el par trasero para tracción.



*Figura 2. Esquema de conexiones de los sensores ultrasónicos y actuadores con la micro:bit.*

<img width="960" height="1280" alt="foto 2" src="https://github.com/user-attachments/assets/b732090d-2a54-49ed-a90b-3850d31cb34b" />


---

## Arquitectura de Software y Lógica

La micro:bit ejecuta un bucle de alta frecuencia programado en Python dentro de Zitronik que procesa las entradas del entorno para determinar las salidas de tracción y viraje.

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

Para mantener la posición centrada en la pista, el algoritmo evalúa de forma continua el diferencial entre los sensores laterales.

```text
Error = Distancia_Izquierda - Distancia_Derecha
Angulo_Direccion = Centro + Kp * Error + Kd * Delta_Error

```

* **Sensor Frontal** Umbral de seguridad para reducir la velocidad o activar maniobras de evitación cuando la distancia frontal cae por debajo del límite crítico.
* **Sensores Laterales** Proveen los datos de corrección fina al eje delantero para ajustar la trayectoria sobre la línea central del carril.

---

## Bitácora de Desarrollo e Iteraciones

El archivo `docs/bitacora-ingenieria.md` registra el proceso completo de diseño y construcción del robot. Comenzamos armando la base física en LEGO Technic para fijar la estructura, calibrar el juego libre del servo delantero y acoplar la tracción trasera. Continuamos con la ubicación física de los tres sensores ultrasónicos, probando alturas y ángulos en la estructura hasta eliminar la reflexión de la señal contra el suelo.

Posteriormente programamos el firmware en Python usando Zitronik, desarrollando funciones para leer los tres sensores al mismo tiempo e incorporando un filtro de software que descarta picos de ruido en las lecturas. Finalmente realizamos las pruebas directas sobre la pista oficial WRO 2026, ajustando las constantes de control PID en el código y sincronizando la velocidad del motor trasero con la dirección para lograr vueltas autónomas y fluidas.

---

## Guía de Instalación y Carga del Código

### Entorno Sugerido

* **Plataforma de Desarrollo** Entorno Zitronik Python para micro:bit
* **Conexión de Hardware** Cable USB a micro USB

### Pasos para Cargar

1. Abrir el entorno Zitronik e importar el proyecto.
2. Cargar el script principal alojado en `src/main.py`.
3. Conectar la tarjeta BBC micro:bit al computador mediante el cable USB.
4. Flashear y transferir la rutina Python cargada directamente a la tarjeta.

<img width="960" height="1280" alt="foto 3" src="https://github.com/user-attachments/assets/96fa9633-75de-42ad-a84f-dd72c8638346" />


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
    ├── main.py                 <-- Código fuente en Python Zitronik para micro:bit
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

<img width="300" height="362" alt="logo-pie-2" src="https://github.com/user-attachments/assets/53826668-2dac-4a69-8642-56ec9496b9e1" />
