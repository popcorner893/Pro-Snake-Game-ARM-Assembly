
![Snake Game Banner](Start_Screen.png)

# 🐍 Snake Game - ARMv4 Assembly Edition

¡Bienvenido al clásico juego de la Serpiente (Snake) programado íntegramente en lenguaje ensamblador **ARMv4** a bajo nivel (Bare-metal)! Este proyecto aprovecha las interrupciones de hardware, temporizadores y manejo directo de memoria para ofrecer una experiencia fluida, con múltiples modos de juego, audio y soporte multijugador.

Diseñado idealmente para ejecutarse en simuladores como **CPUlator** o en placas FPGAs físicas como la **Altera DE1-SoC**.

---

## 🎮 Modos de Juego

El juego cuenta con un menú interactivo que permite elegir entre tres emocionantes modos principales:

*   🍎 **Clásico (Modo 1):** La experiencia tradicional. Come manzanas para crecer y sumar puntos. Evita chocar contra las paredes o contra ti mismo.
*   ⚔️ **1 vs 1 (Modo 2):** ¡Modo competitivo local! Dos jugadores se enfrentan en la misma pantalla. Cada serpiente tiene sus propios controles y puntajes. Si chocas, mueres y le das el punto a tu rival.
*   🌋 **Supervivencia (Modo 3):** Pon a prueba tus reflejos. El mapa de juego se va reduciendo progresivamente con un muro de **lava** a medida que avanza el tiempo. ¡Aparecen alertas de peligro antes de que la lava avance y te corte la cola si te quedas fuera de la zona segura!

## 🎚️ Niveles de Dificultad

Puedes configurar la velocidad del juego desde el menú principal. La dificultad actual se refleja en tiempo real en los LEDs rojos de la placa:

*   🟢 **Fácil (1):** Velocidad reducida, ideal para practicar. *(1 LED encendido)*
*   🟡 **Normal (2):** Velocidad balanceada estándar. *(2 LEDs encendidos)*
*   🔴 **Difícil (3):** Velocidad extrema, el tiempo de reacción es mínimo. *(3 LEDs encendidos)*

## ⌨️ Controles

El juego lee directamente los *scancodes* del **Teclado PS/2** mediante interrupciones de hardware, implementando un filtro anti-rebote.

**Navegación del Menú:**
*   `1`, `2`, `3`: Seleccionar Modo de Juego.
*   `5`: Menú de Dificultad.
*   `E`: Menú de Información/Instrucciones y para volver al menú general.
*   `Enter`: Reiniciar partida tras un *Game Over*.
*   `Espacio`: Volver al menú principal durante una partida.

**Controles Jugador 1:**
*   ⬆️ `Flecha Arriba` / ⬇️ `Flecha Abajo` / ⬅️ `Flecha Izquierda` / ➡️ `Flecha Derecha`

**Controles Jugador 2 (Modo 1v1):**
*   `W` (Arriba) / `S` (Abajo) / `A` (Izquierda) / `D` (Derecha)

## 🎵 Audio y Sonido

El controlador de audio está implementado usando interrupciones de *Timer* para mantener el flujo constante en los búferes FIFO (Left/Right data):
*   **Música de Fondo (BGM):** Música en *loop* continuo mientras juegas.
*   **Efectos de Sonido (SFX):** Inyección de datos de audio directa al hardware para obtener **latencia cero** al comer manzanas o al reproducir la música de *Game Over*, pausando temporalmente la música de fondo para luego retomarla donde se quedó.

## 🖥️ Integración de Hardware Periférico

El código está meticulosamente diseñado para interactuar con los periféricos mapeados en memoria:
*   **Salida VGA (320x240):** Dibujado de píxeles para generar las texturas, fondos dinámicos, serpientes, manzanas y lava. También incluye la renderización del puntaje directamente en pantalla a base de bloques.
*   **Displays de 7-Segmentos:** Los puntajes se muestran en tiempo real convirtiendo valores decimales y mapeándolos con tablas hexadecimales.
*   **LEDs Rojos:** Indicador visual directo de la configuración de dificultad actual.
*   **Temporizador de Intervalo (Timer):** Configurado a 100Hz (10ms) para controlar los *ticks* lógicos del juego y el rellenado del búfer de audio de manera asíncrona.
*   **Controlador Genérico de Interrupciones (GIC):** Manejo jerárquico del teclado (IRQ 79) y temporizador (IRQ 72).

## 🧠 Detalles Técnicos y Lógica Algorítmica

*   🎲 **Generación Pseudo-Aleatoria LCG:** La posición de las manzanas se calcula usando un Algoritmo Congruencial Lineal. La "semilla" se captura de manera dinámica leyendo el estado del Timer en el instante exacto en que el jugador pulsa la tecla para empezar, asegurando una distribución impredecible.
*   🔄 **Búferes Circulares:** El cuerpo de la serpiente (hasta 256 segmentos) se gestiona matemáticamente a través de arreglos indexados con enmascaramiento (`AND rX, #0xFF`), logrando que la serpiente se mueva sin tener que reescribir todos los vectores de su cuerpo en cada *frame*.
*   💥 **Motor de Colisiones:** Sistema de *hitboxes* que determina colisiones con paredes, colisiones de cabeza a cuerpo (contra sí mismo), colisiones contra el rival en el Modo 1v1 y verificación para que las manzanas no aparezcan ni sobre los jugadores ni en la zona de "lava".

---

## 🚀 Cómo Ejecutar el Proyecto

1. Abre un entorno que emule la arquitectura **Altera DE1-SoC ARMv4** (Por ejemplo, [CPUlator](https://cpulator.01xz.net/?sys=arm-de1soc)).
2. Carga el código fuente `.s` en el editor principal del simulador.
3. Asegúrate de tener incluidos todos los recursos de imagen y audio que acompañan el código fuente (ya están incluidos dentro del código mediante bloques de datos `.word` o `.include`).
4. Compila y ejecuta el simulador. ¡Disfruta!

---
*Desarrollado con ❤️ y mucha paciencia en Ensamblador.*
