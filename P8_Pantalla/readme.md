#Objetivo
Configurar y utilizar una pantalla OLED (basada en el controlador SSD1306) con una Raspberry Pi
utilizando Python. Los estudiantes aprenderán a mostrar información básica como texto, símbolos y
valores dinámicos usando buenas prácticas de programación y diseño modular.

#Diagrama de Conexión
Pantalla OLED (I2C) ↔ Raspberry Pi
OLED Pin Conectar a Raspberry Pi
VCC 3.3V (Pin 1)
GND GND (Pin 6)
SCL GPIO3 - SCL1 (Pin 5)
SDA GPIO2 - SDA1 (Pin 3)
scss
CopiarEditar
Raspberry Pi GPIO Header (vista superior)
(3V3) [1] [2] (5V)
(SDA) [3] [4] (5V)
(SCL) [5] [6] (GND)

#Archivo: display_manager.py
# display_manager.py
import board
import busio
import adafruit_ssd1306
from PIL import Image, ImageDraw, ImageFont
class OLEDDisplay:
def __init__(self, width=128, height=64):
self.width = width
self.height = height
self.i2c = busio.I2C(board.SCL, board.SDA)
self.display = adafruit_ssd1306.SSD1306_I2C(width, height, self.i2c)
self.display.fill(0)
self.display.show()
self.image = Image.new("1", (width, height))
self.draw = ImageDraw.Draw(self.image)
self.font = ImageFont.load_default()
def clear(self):
self.draw.rectangle((0, 0, self.width, self.height), outline=0, fill=0)
def show_text(self, text, x=0, y=0):
self.clear()
self.draw.text((x, y), text, font=self.font, fill=255)
self.display.image(self.image)
self.display.show()
def show_logo(self, path):

self.clear()
logo = Image.open(path).convert("1").resize((self.width, self.height))
self.display.image(logo)
self.display.show()

#Archivo: utils.py
# utils.py
import time
def get_timestamp():
return time.strftime("%Y-%m-%d %H:%M:%S")

#Archivo: main.py
# main.py
from display_manager import OLEDDisplay
from utils import get_timestamp
import time
def main():
oled = OLEDDisplay()
oled.show_text("Inicializando...")
time.sleep(1)
# Mostrar logo (opcional)
# oled.show_logo("assets/logo.pbm")
# time.sleep(2)
try:
while True:
now = get_timestamp()
oled.show_text(f"Hora:\n{now}")
time.sleep(1)
except KeyboardInterrupt:
oled.clear()
print("Programa finalizado.")
if __name__ == "__main__":
main()
