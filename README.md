# Sistema de Efectos de Audio Digital en Tiempo Real
**Proyecto Final - Electronica Digital 3**

## Descripcion del Proyecto
Este repositorio contiene el codigo fuente y la documentacion para el desarrollo de un procesador de efectos de audio digital en tiempo real basado en el microcontrolador **NXP LPC1769 (ARM Cortex-M3)**. 

El objetivo principal de este proyecto es implementar algoritmos de Procesamiento Digital de Señales (DSP) para aplicar efectos de sonido (como Eco, Distorsion, o demas) a una señal de audio entrante, garantizando una latencia minima y un rendimiento de nivel industrial mediante el uso intensivo del Acceso Directo a Memoria (DMA).

## Arquitectura y Uso de Perifericos
Mientras la CPU procesa matematicamente un bloque de audio, el hardware se encarga de capturar el siguiente bloque y reproducir el anterior simultaneamente. 

Para lograr esto, integramos los siguientes perifericos:

* **ADC (Convertidor Analogico-Digital):** Se encarga de recibir la señal de audio analogica proveniente del microfono. Previamente, esta señal es acondicionada mediante circuiteria externa (amplificacion y suma de un offset de DC de 1.65V) para adecuarla a los niveles logicos del microcontrolador (0V - 3.3V). El ADC toma muestras a una frecuencia constante.
* **DMA (Acceso Directo a Memoria):** Es el motor principal del flujo de datos. Utilizando la tecnica de Ping-Pong Buffering, un canal DMA captura las muestras digitales del ADC y las guarda directamente en una posicion de la memoria RAM. Una vez que el bloque de datos es modificado por la CPU para aplicar el efecto de sonido, otro canal DMA toma ese bloque de la RAM y lo saca hacia el DAC sin consumir ciclos de reloj del procesador.
* **DAC / AOUT (Convertidor Digital-Analogico):** Recibe el flujo continuo de muestras de audio ya procesadas directamente desde el DMA y las convierte de nuevo en una forma de onda analogica. Esta señal pasa por un filtro pasa-bajos externo para ser reproducida finalmente en un parlante o auriculares.
* **TIMER:** Actua como el director de orquesta del muestreo. Genera los disparos (triggers) periodicos exactos para que el ADC tome las muestras a una frecuencia estricta (por ejemplo, 22 kHz o 44.1 kHz), asegurando la fidelidad del audio.
* **Teclado Matricial (GPIO):** Proporciona la interfaz de control fisico. El usuario utiliza el teclado para navegar por los menus, seleccionar el efecto de sonido deseado (Clean, Delay, Fuzz) y ajustar parametros en tiempo real como la ganancia o el tiempo de retardo.
* **Pantalla LCD (I2C/GPIO):** Proporciona retroalimentacion visual al usuario. Muestra el estado actual del sistema, el efecto que se esta aplicando en tiempo real y el valor de los parametros configurables.

## Estructura del Repositorio
* `/src`: Archivos de codigo fuente en C (Main, inicializacion del sistema y algoritmos DSP).
* `/inc`: Archivos de cabecera (.h) con las definiciones y prototipos.
* `/drivers`: Librerias de abstraccion de hardware para configurar directamente los registros de ADC, DAC, DMA, Timers, etc.
* `/docs`: Esquemas electronicos del acondicionamiento de señal (Offset y Filtros) y hojas de datos.