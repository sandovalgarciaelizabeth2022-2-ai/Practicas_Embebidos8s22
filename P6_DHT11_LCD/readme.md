# Monitoreo de Temperatura y Humedad con DHT11 y LCD 16x2 en Raspberry Pi

## Materiales Requeridos

| Cantidad | Componente |
| :---: | :--- |
| 1 | Raspberry Pi (modelo 3/4) |
| 1 | Sensor DHT11 |
| 1 | Pantalla LCD 16x2 (con módulo adaptador I2C) |
| 1 | Resistencia 10kΩ (para el Pull-up del DHT11) |
| 1 | Protoboard |
| - | Cables jumper (Macho-Macho / Macho-Hembra) |
| 1 | Fuente de alimentación 5V para Raspberry Pi |

###  Conexión del Sensor DHT11
```text
[DHT11]
VCC  -> 5V (Pin 2)
GND  -> GND (Pin 6)
DATA -> GPIO17 (Pin 11)
|
+--[10kΩ]---> 5V (Pull-up)



import adafruit_dht
import board

class DHT11Sensor:
    def __init__(self, pin=board.D17):
        self.sensor = adafruit_dht.DHT11(pin)

    def read_data(self):
        try:
            temperature = self.sensor.temperature
            humidity = self.sensor.humidity
            return temperature, humidity
        except RuntimeError as e:
            print(f"[WARN] Lectura fallida: {e}")
            return None, None
from RPLCD.i2c import CharLCD

class LCDDisplay:
    def __init__(self, address=0x27):
        self.lcd = CharLCD('PCF8574', address)
        self.lcd.clear()

    def show_message(self, line1, line2=""):
        self.lcd.clear()
        self.lcd.write_string(line1.ljust(16))
        if line2:
            self.lcd.crlf()
            self.lcd.write_string(line2.ljust(16))

import time

def delay(seconds):
    try:
        time.sleep(seconds)
    except KeyboardInterrupt:
        print("Interrupción del usuario.")
