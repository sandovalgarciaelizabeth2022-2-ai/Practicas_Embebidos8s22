# Práctica 4: Recorrido de LED con PWM

## Objetivo
Implementar un efecto de recorrido de LEDs utilizando modulación por ancho de pulso (PWM) para crear transiciones suaves de encendido y apagado.

---

## Materiales
- Raspberry Pi
- 5 LEDs
- 5 resistencias
- Protoboard
- Cables jumper

---

## Descripción
En esta práctica se utiliza PWM (Pulse Width Modulation) para controlar la intensidad de los LEDs.

Cada LED aumenta y disminuye su brillo de forma gradual (efecto fade), creando un recorrido suave hacia adelante y hacia atrás usando un vector de pines GPIO.

---

## Código

```python
#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time

LEDS = [17, 27, 22, 23, 24]

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

pwms = []

for led in LEDS:
    GPIO.setup(led, GPIO.OUT)
    pwm = GPIO.PWM(led, 1000)
    pwm.start(0)
    pwms.append(pwm)

def fade(pwm):
    for dc in range(0, 101, 5):
        pwm.ChangeDutyCycle(dc)
        time.sleep(0.01)
    for dc in range(100, -1, -5):
        pwm.ChangeDutyCycle(dc)
        time.sleep(0.01)

try:
    while True:

        for pwm in pwms:
            fade(pwm)

        for pwm in reversed(pwms):
            fade(pwm)

except KeyboardInterrupt:
    pass

finally:
    for pwm in pwms:
        pwm.stop()
    GPIO.cleanup()
