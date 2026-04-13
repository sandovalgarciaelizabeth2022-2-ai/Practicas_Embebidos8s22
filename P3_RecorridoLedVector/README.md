# Práctica 3: Recorrido de LED en Vector

## Objetivo
Controlar múltiples LEDs utilizando un arreglo (vector) para crear un efecto de recorrido de luz hacia adelante y hacia atrás.

---

## Materiales
- Raspberry Pi
- 5 LEDs
- 5 resistencias
- Protoboard
- Cables jumper

---

## Descripción
En esta práctica se utiliza una lista (vector) para almacenar los pines GPIO donde están conectados los LEDs.

El programa enciende los LEDs uno por uno en orden, creando un efecto de desplazamiento. Después repite el proceso en sentido inverso, simulando un recorrido de ida y vuelta.

---

## Código

```python
#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time

LEDS = [17, 27, 22, 23, 24]

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

for led in LEDS:
    GPIO.setup(led, GPIO.OUT)
    GPIO.output(led, GPIO.LOW)

try:
    while True:

        for led in LEDS:
            GPIO.output(led, GPIO.HIGH)
            time.sleep(0.3)
            GPIO.output(led, GPIO.LOW)

        for led in reversed(LEDS):
            GPIO.output(led, GPIO.HIGH)
            time.sleep(0.3)
            GPIO.output(led, GPIO.LOW)

except KeyboardInterrupt:
    pass

finally:
    GPIO.cleanup()
