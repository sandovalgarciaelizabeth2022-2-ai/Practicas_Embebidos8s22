# Práctica 1: Led Blink

## Objetivo
Encender y apagar un LED usando GPIO.

## Código

```python
#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time

LED_PIN = 18  # GPIO 18 (pin físico 12)

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(LED_PIN, GPIO.OUT, initial=GPIO.LOW)

while True:
    GPIO.output(LED_PIN, GPIO.HIGH)
    time.sleep(1)
    GPIO.output(LED_PIN, GPIO.LOW)
    time.sleep(1)
