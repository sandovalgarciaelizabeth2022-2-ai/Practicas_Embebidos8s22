#Objetivo
Implementar un sistema de monitoreo ambiental con sensor DHT11.
Visualizar datos en una pantalla OLED SSD1306.
Aplicar principios de programación modular, gráficos en tiempo real y manejo de errores.
#Materiales
Raspberry Pi con GPIO habilitado
Sensor DHT11
Pantalla OLED SSD1306 (128x64) vía I2C
Jumpers y protoboard
Íconos temp.bmp y hum.bmp (blanco y negro, 24x24 px)
Python 3, librerías adafruit_dht, adafruit_ssd1306, Pillow

#Diagrama de Conexiones

DHT11        Raspberry Pi
-----        -------------
VCC   ------ 3.3V (pin 1)
DATA  ------ GPIO17 (pin 13)
GND   ------ GND (pin 6)
OLED SSD1306   Raspberry Pi
-------------  -------------
VCC   -------- 3.3V (pin 1)
GND   -------- GND (pin 9)
SCL   -------- SCL (pin 5)
SDA   -------- SDA (pin 3)

# Estructura de Archivos Recomendada

sensor_display_project/
│
├── main.py
├── config.py
├── sensor_dht.py
├── oled_visualizer.py
├── utils.py
├── icons/
│   ├── temp.bmp
│   └── hum.bmp
└── README.md


config.py — Configuraciones globales

import board

# Pines
I2C_SCL = board.SCL
I2C_SDA = board.SDA
DHT_PIN = board.D17

# Pantalla
OLED_WIDTH = 128
OLED_HEIGHT = 64

# Parámetros
TEMP_MAX = 50
HUM_MAX = 100
HIST_MAX_POINTS = 128
REFRESH_DELAY = 2  # segundos
ANIM_LIMIT = 2

# Rutas de íconos
ICON_TEMP_PATH = "icons/temp.bmp"
ICON_HUM_PATH = "icons/hum.bmp"


utils.py — Funciones auxiliares

def escalar(valor, valor_maximo, alto):
    return int((1 - valor / valor_maximo) * alto)


sensor_dht.py — Clase del sensor

import adafruit_dht
from config import DHT_PIN

class SensorDHT:
    def __init__(self, pin=DHT_PIN):
        self.dht = adafruit_dht.DHT11(pin)

    def leer(self):
        try:
            temp = self.dht.temperature
            hum = self.dht.humidity
            if temp is not None and hum is not None:
                return temp, hum
            else:
                raise ValueError("Lectura inválida del sensor.")
        except RuntimeError as e:
            print("Error de lectura DHT11:", e.args[0])
            return None, None

    def cerrar(self):
        self.dht.exit()


oled_visualizer.py — Clase de visualización

import adafruit_ssd1306
from PIL import Image, ImageDraw, ImageFont, ImageOps
from config import *
from utils import escalar

class OledVisualizer:
    def __init__(self, i2c, width=OLED_WIDTH, height=OLED_HEIGHT, icon_temp_path=ICON_TEMP_PATH, icon_hum_path=ICON_HUM_PATH):
        self.display = adafruit_ssd1306.SSD1306_I2C(width, height, i2c)
        self.image = Image.new("1", (width, height))
        self.draw = ImageDraw.Draw(self.image)
        self.font = ImageFont.load_default()
        self.icon_temp = Image.open(icon_temp_path).convert("1")
        self.icon_hum = Image.open(icon_hum_path).convert("1")
        self.anim_offset = 0
        self.anim_dir = 1

    def actualizar_animacion(self):
        self.anim_offset += self.anim_dir
        if abs(self.anim_offset) >= ANIM_LIMIT:
            self.anim_dir *= -1

    def limpiar(self):
        self.draw.rectangle((0, 0, OLED_WIDTH, OLED_HEIGHT), outline=0, fill=0)

    def mostrar_datos(self, temp, hum, temp_hist, hum_hist):
        self.limpiar()
        self.actualizar_animacion()

        # Íconos animados
        self.image.paste(self.icon_temp, (0, 0 + self.anim_offset))
        self.image.paste(self.icon_hum, (0, OLED_HEIGHT - 16 - self.anim_offset))

        # Texto
        self.draw.text((20, 0), f"{temp:.1f} C", font=self.font, fill=255)
        self.draw.text((20, OLED_HEIGHT - 12), f"{hum:.1f} %", font=self.font, fill=255)

        # Gráficas
        self._dibujar_historial(temp_hist, TEMP_MAX, 24, 12)
        self._dibujar_historial(hum_hist, HUM_MAX, 24, 35)

        self.display.image(self.image)
        self.display.show()

    def _dibujar_historial(self, datos, valor_max, alto, offset_y):
        for i in range(1, len(datos)):
            x1, x2 = i - 1, i
            y1 = escalar(datos[i - 1], valor_max, alto) + offset_y
            y2 = escalar(datos[i], valor_max, alto) + offset_y
            self.draw.line((x1, y1, x2, y2), fill=255)


main.py — Programa principal

import time
import busio
from config import *
from sensor_dht import SensorDHT
from oled_visualizer import OledVisualizer

def main():
    i2c = busio.I2C(I2C_SCL, I2C_SDA)
    visual = OledVisualizer(i2c)
    sensor = SensorDHT()

    temp_hist = []
    hum_hist = []

    try:
        while True:
            temp, hum = sensor.leer()
            if temp is not None and hum is not None:
                temp_hist.append(temp)
                hum_hist.append(hum)

                if len(temp_hist) > HIST_MAX_POINTS:
                    temp_hist.pop(0)
                    hum_hist.pop(0)

                visual.mostrar_datos(temp, hum, temp_hist, hum_hist)
            else:
                print("Saltando actualización por error de lectura.")

            time.sleep(REFRESH_DELAY)

    except KeyboardInterrupt:
        print("\nEjecución interrumpida por el usuario.")

    except Exception as e:
        print(f"Error inesperado: {e}")

    finally:
        sensor.cerrar()

if __name__ == "__main__":
    main()










