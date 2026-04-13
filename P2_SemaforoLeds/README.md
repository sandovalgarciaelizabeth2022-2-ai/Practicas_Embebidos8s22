
# Práctica 2: Semáforo con LEDs

## Objetivo
Simular el funcionamiento de un semáforo utilizando tres LEDs (verde, amarillo y rojo) controlados por una Raspberry Pi.

---

## Materiales
- Raspberry Pi
- 3 LEDs (verde, amarillo y rojo)
- 3 resistencias
- Protoboard
- Cables jumper

---

## Descripción
En esta práctica se implementa un sistema de semáforo usando la librería GPIO en modo BCM.

El programa controla tres LEDs:
- Verde → Avanzar
- Amarillo → Precaución (parpadeo)
- Rojo → Alto

Además, se muestra una cuenta regresiva en la consola para cada estado del semáforo.

---

## Código

```python
#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time
import sys

VERDE = 17
AMARILLO = 27
ROJO = 22

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

GPIO.setup(VERDE, GPIO.OUT, initial=GPIO.LOW)
GPIO.setup(AMARILLO, GPIO.OUT, initial=GPIO.LOW)
GPIO.setup(ROJO, GPIO.OUT, initial=GPIO.LOW)

def cuenta_regresiva(segundos, texto):
    for i in range(segundos, 0, -1):
        sys.stdout.write(f"\r{texto} - {i} seg   ")
        sys.stdout.flush()
        time.sleep(1)
    print()

def amarillo():
    GPIO.output(VERDE, GPIO.LOW)
    GPIO.output(ROJO, GPIO.LOW)
    GPIO.output(AMARILLO, GPIO.LOW)
    time.sleep(0.5)

    for i in range(3, 0, -1):
        GPIO.output(AMARILLO, GPIO.HIGH)
        sys.stdout.write(f"\rAMARILLO - {i} seg   ")
        sys.stdout.flush()
        time.sleep(1)

        GPIO.output(AMARILLO, GPIO.LOW)
        time.sleep(0.5)

    print()

try:
    while True:

        GPIO.output(VERDE, GPIO.HIGH)
        GPIO.output(AMARILLO, GPIO.LOW)
        GPIO.output(ROJO, GPIO.LOW)
        cuenta_regresiva(60, "VERDE")

        GPIO.output(VERDE, GPIO.LOW)

        amarillo()

        GPIO.output(ROJO, GPIO.HIGH)
        GPIO.output(AMARILLO, GPIO.LOW)
        cuenta_regresiva(45, "ROJO")

except KeyboardInterrupt:
    print("\nPrograma interrumpido")

finally:
    GPIO.cleanup()
    print("GPIO limpiado")
