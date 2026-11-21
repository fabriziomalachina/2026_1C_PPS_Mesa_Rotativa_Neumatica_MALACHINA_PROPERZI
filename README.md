![Logo Institucional](https://github.com/JonatanBogadoUNLZ/PPS-Jonatan-Bogado/blob/9952aac097aca83a1aadfc26679fc7ec57369d82/LOGO%20AZUL%20HORIZONTAL%20-%20fondo%20transparente.png)

# Mesa Rotativa Neumática Automatizada

**Tipo:** PPS (Prácticas Profesionalizantes Supervisadas)  
**Año:** 2026 — **Cuatrimestre:** 1C  

**Carrera:** Ingeniería Mecatrónica — Facultad de Ingeniería (UNLZ)  
**Materia / Curso:** Prácticas Profesionalizantes Supervisadas (PPS)  
**Docente / Cátedra:** [NOMBRE_DOCENTE]  
**Autor/es:** MALACHINA, Fabrizio — PROPERZI, Martín

---

## Introducción / Objetivo

La práctica profesionalizante consiste en el diseño, fabricación e implementación de una mesa rotativa de 6 posiciones accionada mediante actuadores neumáticos, destinada a integrarse con una cinta transportadora ya existente en el laboratorio de automatización. El objetivo principal es desarrollar un sistema automatizado integral, gobernado por un PLC, que permita la coordinación secuencial y sincronizada de los tres subsistemas, incorporando además tecnologías de comunicación industrial modernas basadas en protocolo IO-Link.
El proyecto abarca múltiples áreas de la ingeniería mecatrónica, incluyendo el diseño mecánico (estructura y mecanismo de indexado de la mesa), la neumática (selección y control de actuadores), la automatización industrial (lógica secuencial y control del proceso), la electrónica (interconexión de sensores y actuadores) y las comunicaciones industriales. 


La práctica se iba a apoyar en la donación realizada por la empresa ifm electronic, que incluye un kit maestro IO-Link ZZ1120, el cual integra un master con interfaz industrial (EtherNet/IP), fuente de alimentación, cableado y sensores, permitiendo conectar hasta múltiples dispositivos IO-Link y gestionar señales digitales y analógicas de forma robusta . Asimismo, se incorporan módulos y accesorios como el controlador CR1142, cables de conexión (EVC040, EVC076, EVC815, EVC906, EVC041), módulos de comunicación como EBC116, y sensores como TA2105 y LDH292, que permiten construir una arquitectura de automatización completa y escalable. Por una desición en conjunto con el tutor de la cátedra de automatización industrial, se decidió volcar la etapa de control a un PLC Zelio para facilitar la puesta en marcha de la mesa y que los alumnos de la catedra la puedan aprovechar lo antes posible.

**Objetivo general:**  
Diseñar, construir y automatizar una mesa rotativa neumática de indexado paso a paso comandada por un PLC **Schneider Electric Zelio Logic (SR2B121BD)** y dos válvulas electroneumáticas 5/2 monoestables, sincronizada con una cinta transportadora mediante un final de carrera.

**Objetivos específicos:**
- Fabricar la estructura mecánica (gabinete reciclado, base de aluminio torneada, disco acrílico, mecanismo de gatillo y doble sistema de freno).
- Implementar el circuito electroneumático con freno de seguridad normalmente aplicado (fallo seguro por resorte) y panel frontal con fichas banana identificadas.
- Simular la lógica secuencial en **Festo FluidSIM** y programarla en lenguaje Ladder en **Zelio Soft 2**.
- Validar el funcionamiento integrado de la mesa junto con el PLC Zelio y la cinta transportadora en el Laboratorio de Automatización.

---

## Índice
- [Brief](#brief)
- [Descripción técnica](#descripción-técnica)
- [Arquitectura del sistema](#arquitectura-del-sistema)
- [Instrucciones de uso](#instrucciones-de-uso)
- [Tecnologías utilizadas](#tecnologías-utilizadas)
- [Listado de componentes](#listado-de-componentes)
- [Planificación / Diagrama de Gantt](#planificación--diagrama-de-gantt)
- [Esquemáticos / Planos](#esquemáticos--planos)
- [Fotos / Videos](#fotos--videos)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Autores](#autores)
- [Licencia](#licencia)

---

## Brief

**One-liner:**  
Mesa rotativa electroneumática indexada por PLC Zelio Logic que avanza automáticamente al detectar cada paquete en una cinta transportadora, asistiendo a los operarios de carga y descarga de forma segura y repetible.

**Elevator pitch (30 segundos):**  
Este proyecto **Mesa Rotativa Neumática Automatizada** (**PPS**, **2026 1C**) resuelve el posicionamiento indexado de paquetes desde una cinta transportadora continua mediante un mecanismo neumático de gatillo y freno comandado por PLC. Está orientado a estaciones de carga/descarga industrial y a prácticas en el Laboratorio de Automatización de la FI-UNLZ, logrando ciclos repetibles, frenado seguro ante cortes de energía y conexión rápida por fichas banana. Se implementa con actuadores neumáticos ISO 6431, válvulas 5/2 monoestables, sensores de posición y PLC **Zelio Logic SR2B121BD**, y se valida mediante simulación en FluidSIM y pruebas reales en laboratorio.

### Problema
- **Contexto:** Línea de transferencia con cinta transportadora continua / Laboratorio de Automatización (FI-UNLZ).
- **Dolor principal:** Sin un sistema de indexado y frenado sincronizado, el operario debe posicionar la mesa manualmente o la misma se desplaza por inercia al recibir/retirar paquetes.
- **Impacto:** Ergonomía, repetibilidad del posicionamiento, ritmo de trabajo y seguridad operativa.

### Solución propuesta
- **Qué hace:**
  - Mantiene la cinta transportadora en marcha continua tras pulsar `MARCHA`.
  - Detecta cada paquete mediante un final de carrera (`FDC_CINTA`) y ejecuta automáticamente el ciclo secuencial **`B+` → `A+` → `B-` → `A-`**.
  - Libera el freno (`B+`), hace girar la mesa a la siguiente posición con el gatillo (`A+`), aplica el freno mientras el gatillo aún sostiene la mesa (`B-`) y luego retrae el gatillo (`A-`).
  - Detiene la cinta y resetea todas las etapas ante una parada/emergencia (`PARADA`), aplicando el freno por resorte (fallo seguro).
- **Cómo lo hace (alto nivel):** Final de carrera + sensores de posición (`SA-`, `SA+`, `SB-`, `SB+`) → PLC Zelio Logic (lógica paso a paso `M1`–`M4`) → válvulas electroneumáticas 5/2 monoestables (`A+`, `B+`) y contactor de cinta (`Q3`) → cilindros neumáticos de gatillo y freno.
- **Valor diferencial:** Arquitectura de fallo seguro en el freno (sin energía el freno queda aplicado), estructura construida a partir de materiales reciclados y panel frontal con fichas banana para interconexión modular en laboratorio.

### Alcance
**Incluye:**
- Diseño y fabricación mecánica completa de la mesa rotativa, gatillo y doble freno.
- Cableado eléctrico, identificación de señales y panel frontal con fichas banana de 4 mm.
- Simulación electroneumática en Festo FluidSIM (`Codigo/PPS.ct`).
- Programa Ladder para PLC Zelio Logic `SR2B121BD` (`Codigo/PPS.zm2`).
- Pruebas de funcionamiento integradas con PLC y cinta transportadora.

**No incluye (por ahora):**
- Detección de flanco / memoria de cola para paquetes múltiples acumulados sobre `FDC_CINTA`.
- Barrera óptica o enclavamiento de presencia de manos del operario.
- Supervisión remota / SCADA o conteo de producción en HMI.

### Estado del proyecto
- **Madurez:** Prototipo funcional validado en laboratorio.
- **Qué funciona hoy:** Estructura mecánica completa, tablero con fichas banana, secuencia electroneumática en FluidSIM, programa Ladder en Zelio Soft 2 y pruebas físicas con cinta transportadora.
- **Próximos pasos:** [Completar si hay mejoras pendientes, ej. agregar reguladores de caudal o informe PDF final].

### Demo rápida
- **Video de funcionamiento:** [Ver video en Multimedia](Multimedia%20%28Im%C3%A1genes%20y%20videos%29/video%20de%20funcionamiento.mp4)
- **Instrucciones express:**
  1. Alimentar con 24 V DC y aire comprimido regulado a [presión de trabajo, ej. 4–6 bar].
  2. Verificar que la mesa esté en reposo (`SA-` y `SB-` activos) y presionar `MARCHA` (`I1`) para encender la cinta (`Q3`).
  3. Al pasar un paquete y accionar `FDC_CINTA` (`I3`), la mesa avanza automáticamente una posición y vuelve a quedar frenada.

---

## Descripción técnica

El sistema coordina dos actuadores neumáticos de doble efecto mediante válvulas electroneumáticas **5/2 monoestables (retorno por resorte)** de 24 V DC:

1. **Pistón A — Gatillo (`A+`):** En reposo está retraído (`SA-`). Al energizarse `A+`, avanza hasta `SA+` empujando el mecanismo de trinquete/gatillo para indexar la mesa a la siguiente posición.
2. **Pistón B — Freno (`B+`):** Sus conexiones neumáticas están invertidas intencionalmente para lograr **fallo seguro**: sin energía (`B+ = 0`), el pistón permanece **extendido frenando la mesa** (`SB-`). Al energizarse `B+`, el freno se retrae hasta `SB+` liberando el giro.

La lógica de control se resolvió con el **método paso a paso** (4 etapas `M1`–`M4` equivalentes a los relés `K1`–`K4` del esquema electroneumático) para evitar solapamientos de señal:

| Etapa | Relé / Marca | Condición de inicio | Salidas activas | Acción física |
|---|---|---|---|---|
| **Habilitación** | `Q3` (`K0`) | `I1` (`MARCHA`) y sin `I2` (`PARADA`) | `Q3` | Enciende la cinta y habilita la mesa |
| **Etapa 1** | `M1` (`K1`) | `SA-` (`I4`) + `FDC_CINTA` (`I3`) + `Q3` | `Q2` (`B+`) | Retrae el freno hasta `SB+` |
| **Etapa 2** | `M2` (`K2`) | `SB+` (`ID`) + `M1` + `Q3` | `Q1` (`A+`) y `Q2` (`B+`) | Avanza el gatillo y gira la mesa hasta `SA+` |
| **Etapa 3** | `M3` (`K3`) | `SA+` (`IB`) + `M2` + `Q3` | `Q1` (`A+`) | Suelta `B+` para frenar la mesa (`SB-`) mientras el gatillo la sostiene |
| **Etapa 4** | `M4` (`K4`) | `SB-` (`IC`) + `M3` + `Q3` | *(ninguna)* | Suelta `A+` para retraer el gatillo hasta `SA-` |
| **Reset general** | `RM1`–`RM4`, `RQ3` | `I2` (`PARADA`) | Todas en `0` | Detiene cinta, retrae gatillo y aplica freno |

---

## Arquitectura del sistema

**Entradas (PLC Zelio Logic `SR2B121BD` — 24 V DC):**
- `I1` — `MARCHA`: Pulsador de marcha de la cinta y habilitación del sistema.
- `I2` — `PARADA`: Pulsador de parada / emergencia (reset general de etapas y cinta).
- `I3` — `FDC CINTA`: Final de carrera ubicado sobre la cinta transportadora.
- `I4` — `SA-`: Sensor de posición gatillo retraído (activo en reposo).
- `IB` — `SA+`: Sensor de posición gatillo extendido.
- `IC` — `SB-`: Sensor de posición freno aplicado (activo en reposo).
- `ID` — `SB+`: Sensor de posición freno liberado.

**Procesamiento / Control:**
- PLC **Schneider Electric Zelio Logic `SR2B121BD`** programado en lenguaje **Ladder (LD)** en **Zelio Soft 2** (`Codigo/PPS.zm2`).

**Salidas (relés 24 V DC):**
- `Q1` — `bobina A+`: Electroválvula 5/2 monoestable del pistón Gatillo (`M2` OR `M3`).
- `Q2` — `bobina B+`: Electroválvula 5/2 monoestable del pistón Freno (`M1` OR `M2`).
- `Q3` — `CINTA`: Contactor de alimentación de la cinta transportadora.

**Interfaz de conexión:**
- Panel frontal en gabinete metálico con fichas banana de 4 mm codificadas por color (rojo `+`, negro `-`, señal) y rotuladas (`SA-`, `SA+`, `SB-`, `SB+`, `A+`, `B+`).

---

## Instrucciones de uso

### Requisitos previos
- **Software:**
  - **Zelio Soft 2** (Schneider Electric) para abrir, simular o transferir `Codigo/PPS.zm2`.
  - **Festo FluidSIM Neumática** para abrir y simular `Codigo/PPS.ct`.
- **Hardware:**
  - PLC Zelio Logic `SR2B121BD` (24 V DC) + cable de programación.
  - Fuente de alimentación de 24 V DC y suministro de aire comprimido filtrado y regulado.
  - Cables con fichas banana de 4 mm para vincular el gabinete con el PLC y la cinta transportadora.

### Instalación / Puesta en marcha
1. Clonar o descargar este repositorio.
2. Abrir `Codigo/PPS.zm2` en **Zelio Soft 2**, verificar el modelo `SR2B121BD` y transferir el programa al PLC *(o probarlo primero en modo Simulación)*.
3. Conectar las entradas (`I1`–`ID`) y salidas (`Q1`–`Q3`) del PLC a las fichas banana frontales del gabinete respetando las etiquetas.
4. Habilitar el suministro de aire comprimido y la alimentación de 24 V DC.
5. Verificar en reposo que los sensores `SA-` (`I4`) y `SB-` (`IC`) estén activos antes de iniciar.

### Uso
- **Modo normal:** Presionar `MARCHA` (`I1`) para poner en marcha la cinta transportadora (`Q3`). Cada vez que un paquete presione `FDC_CINTA` (`I3`), la mesa ejecutará un paso completo de indexado y volverá a quedar frenada.
- **Parada / Emergencia:** Presionar `PARADA` (`I2`) para detener la cinta, resetear todas las memorias (`M1`–`M4`, `Q3`) y devolver ambos cilindros a posición segura.
- **Nota de mantenimiento:** Como indica la etiqueta del gabinete, **desconectar las mangueras neumáticas y purgar el aire antes de abrir la tapa superior**.
- **Nota de cableado real para `I2` (`PARADA`):** El archivo `PPS.zm2` está configurado con `I2` activo en `1` para pruebas rápidas; si en tablero definitivo se cablea el golpe de puño físicamente **NC** (recomendado), invertir `i2` ↔ `I2` en las líneas 001 y 015 antes de descargar al PLC.

---

## Tecnologías utilizadas
- **Automatización / Control:** PLC Schneider Electric Zelio Logic (`SR2B121BD`), lógica Ladder (LD).
- **Neumática / Electroneumática:** Actuadores neumáticos ISO 6431, válvulas 5/2 monoestables 24 V DC, lógica paso a paso.
- **Simulación y Software:** Zelio Soft 2, Festo FluidSIM Neumática.
- **Fabricación mecánica:** Torneado y frenteado de aluminio, mecanizado de acrílico, soldadura/armado de estructura metálica reciclada.

---

## Listado de componentes

| Componente | Cantidad | Modelo / Especificación | Función |
|---|---:|---|---|
| PLC | 1 | Schneider Electric Zelio Logic `SR2B121BD` (24 V DC, 8E/4S) | Control lógico secuencial del sistema |
| Cilindro neumático (Pistón A) | 1 | ISO 6431 Serie DNC / SI — Doble efecto (ver `Datasheet/`) | Accionamiento del gatillo de giro de la mesa |
| Cilindro neumático (Pistón B) | 1 | Cilindro compacto doble efecto — [Modelo exacto] | Accionamiento del freno de la mesa |
| Válvula electroneumática | 2 | 5/2 vías, monoestable, bobina 24 V DC — [Modelo exacto] | Comando de pistones A y B |
| Sensores de posición | 4 | Inductivos / magnéticos 24 V DC — [Modelo exacto] | Detección de extremos `SA-`, `SA+`, `SB-`, `SB+` |
| Final de carrera | 1 | Mecánico con palanca/rodillo — [Modelo exacto] | Detección de paquetes sobre la cinta (`FDC_CINTA`) |
| Pulsadores de mando | 2 | Marcha (NA) + Parada/Emergencia golpe de puño | Inicio y parada de emergencia del sistema |
| Disco rotativo y base | 1 | Acrílico mecanizado + maza de aluminio torneada | Superficie porta-paquetes y cubo de indexado |
| Estructura y gabinete | 1 | Gabinete metálico reciclado con estructura y patas regulables | Soporte mecánico y alojamiento del circuito |
| Conectores y neumática | Varios | Fichas banana 4 mm, mangueras, racores, bulonería | Interconexión eléctrica y neumática |

---

## Planificación / Diagrama de Gantt

Cronograma de actividades realizadas entre **mayo y noviembre de 2026** (total **168 horas-hombre**, 84 hs por integrante):

![Diagrama de Gantt PPS Mesa Rotativa](Informes%20%28GANTT%2C%20INFORME%20PDF%2C%20CRONOGRAMAS%2C%20MANUALES%29/Diagrama_Gantt_PPS_Mesa_Rotativa.png)

---

## Esquemáticos / Planos
- Circuito electroneumático y neumático (Festo FluidSIM) → [`Codigo/PPS.ct`](Codigo/PPS.ct)
- Programa Ladder del PLC Zelio Logic (`SR2B121BD`) → [`Codigo/PPS.zm2`](Codigo/PPS.zm2)
- Datasheet cilindros ISO 6431 → [`Datasheet/Cilindros-neumaticos-ISO-6431-serie-DNC-Y-SI.pdf`](Datasheet/Cilindros-neumaticos-ISO-6431-serie-DNC-Y-SI.pdf)
- Planos mecánicos / esquemáticos adicionales → [Agregar archivos en `Planos/` si corresponde]

---

## Fotos / Videos

### Vista frontal y panel de conexión (fichas banana)
![Vista frontal de la mesa rotativa y panel de fichas banana](Multimedia%20%28Im%C3%A1genes%20y%20videos%29/1.jpeg)

### Vista general en Laboratorio de Automatización
![Vista general en laboratorio](Multimedia%20%28Im%C3%A1genes%20y%20videos%29/2.jpeg)

### Vista superior — Mecanismo de gatillo (Pistón A), freno (Pistón B) y maza de aluminio
![Vista superior del mecanismo neumático](Multimedia%20%28Im%C3%A1genes%20y%20videos%29/3.jpeg)

### Video de funcionamiento
- [Descargar / ver `video de funcionamiento.mp4`](Multimedia%20%28Im%C3%A1genes%20y%20videos%29/video%20de%20funcionamiento.mp4)

---

## Estructura del repositorio
- `Codigo/` — Simulación electroneumática FluidSIM (`PPS.ct`) y programa PLC Zelio Soft 2 (`PPS.zm2`).
- `Datasheet/` — Hojas de datos y especificaciones técnicas de los componentes.
- `Informes (GANTT, INFORME PDF, CRONOGRAMAS, MANUALES)/` — Diagrama de Gantt, informes y documentación.
- `Multimedia (Imágenes y videos)/` — Fotografías del armado y video de funcionamiento.
- `Planos/` — Planos mecánicos y esquemáticos.

---

## Autores
- **MALACHINA, Fabrizio** — [Legajo] — [mail / LinkedIn opcional]
- **PROPERZI, Martín** — [Legajo] — [mail / LinkedIn opcional]

---

## Licencia
[Definir según la cátedra: Uso académico — FI-UNLZ / MIT]

---

## About (descripción corta del repositorio)

Texto sugerido para el campo **About** de GitHub:

**PPS — Mesa Rotativa Neumática Automatizada — FI-UNLZ — 2026 1C — Malachina, Properzi**
