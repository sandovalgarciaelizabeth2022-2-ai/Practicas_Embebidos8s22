bcm.py


#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time

LED_PIN = 18  # GPIO 18 (pin físico 12)

try:
    # Configuración
    GPIO.setmode(GPIO.BCM)
    GPIO.setwarnings(False)
    GPIO.setup(LED_PIN, GPIO.OUT, initial=GPIO.LOW)

    print("Control de LED iniciado (Modo BCM)")
    print(f"Usando GPIO {LED_PIN} (Pin físico 12)")
    print("Presiona Ctrl+C para salir")

    # Loop principal
    while True:
        GPIO.output(LED_PIN, GPIO.HIGH)
        print("LED ENCENDIDO")
        time.sleep(1)

        GPIO.output(LED_PIN, GPIO.LOW)
        print("LED APAGADO")
        time.sleep(1)

except KeyboardInterrupt:
    print("\nPrograma interrumpido por el usuario")

finally:
    GPIO.cleanup()
    print("GPIO limpiado. Programa finalizado.")
