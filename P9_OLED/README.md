#Objetivo General
Configurar y programar una pantalla OLED utilizando una Raspberry Pi y Python para mostrar texto
desplazable (scrolling), aplicando buenas prácticas de desarrollo de software embebido.

Diagrama de Conexión
OLED SSD1306 (I2C) Raspberry Pi GPIO
------------------- -------------------
VCC 3.3V (Pin 1)
GND GND (Pin 6)
SCL SCL (Pin 5 - GPIO3)
SDA SDA (Pin 3 - GPIO2)

️ #Configuración del Entorno
1. Actualizar el sistema operativo:
sudo apt update && sudo apt upgrade -y
2. Habilitar I2C:
sudo raspi-config
Navegar a Interfacing Options > I2C > Habilitar.
3. Instalar librerías necesarias:
sudo apt install python3-pip -y
pip3 install adafruit-circuitpython-ssd1306
pip3 install pillow
4. Verificar dirección I2C del OLED:

sudo apt install i2c-tools
i2cdetect -y 1
Deberías ver una dirección como 3C.

 Código Python (Enfoque Modular)
 Estructura de Archivos
oled_scrolling/
├── main.py
├── oled_display.py
└── config.py

config.py – Configuración general
# config.py
I2C_ADDRESS = 0x3C
WIDTH = 128
HEIGHT = 64
SCROLL_SPEED = 0.05 # segundos entre movimientos
MESSAGE = "Hola desde Raspberry Pi con pantalla OLED. Esto es un scrolling de texto.
"

oled_display.py – Lógica de pantalla
# oled_display.py
import board
import busio
from PIL import Image, ImageDraw, ImageFont
import adafruit_ssd1306
import time
from config import I2C_ADDRESS, WIDTH, HEIGHT, SCROLL_SPEED, MESSAGE
class OLEDScroller:
def __init__(self):
self.i2c = busio.I2C(board.SCL, board.SDA)
self.display = adafruit_ssd1306.SSD1306_I2C(WIDTH, HEIGHT, self.i2c,
addr=I2C_ADDRESS)
self.display.fill(0)
self.display.show()
self.image = Image.new("1", (WIDTH * 2, HEIGHT)) # Doble ancho para
scrolling
self.draw = ImageDraw.Draw(self.image)
self.font = ImageFont.load_default()
#Hay un error en la función texsize(), Este error se debe a que en versiones
#recientes de Pillow (>=10.0.0), el método textsize() fue eliminado y reemplazado
#por textbbox() o textlength().
def scroll_textOLD(self, text):
self.draw.rectangle((0, 0, WIDTH * 2, HEIGHT), outline=0, fill=0)
self.draw.text((0, 0), text, font=self.font, fill=255)
text_width, _ = self.draw.textsize(text, font=self.font)

for x in range(0, text_width + 1):
cropped = self.image.crop((x, 0, x + WIDTH, HEIGHT))
self.display.image(cropped)
self.display.show()
time.sleep(SCROLL_SPEED)
##VERSION DE LA FUNCIÓN CORREGIDA
def scroll_text(self, text):
self.draw.rectangle((0, 0, WIDTH * 2, HEIGHT), outline=0, fill=0)
self.draw.text((0, 0), text, font=self.font, fill=255)
bbox = self.draw.textbbox((0, 0), text, font=self.font)
text_width = bbox[2] - bbox[0]
for x in range(0, text_width + 1):
cropped = self.image.crop((x, 0, x + WIDTH, HEIGHT))
self.display.image(cropped)
self.display.show()
time.sleep(SCROLL_SPEED)
def clear(self):
self.display.fill(0)
self.display.show()

main.py – Ejecución del programa
# main.py
from oled_display import OLEDScroller
from config import MESSAGE
def main():
scroller = OLEDScroller()
while True:
scroller.scroll_text(MESSAGE)
if __name__ == "__main__":
try:
main()
except KeyboardInterrupt:
print("\nEjecución finalizada por el usuario.")

 Resultados Esperados
Al ejecutar main.py, el mensaje definido en config.py se desplazará de forma continua en
la pantalla OLED.
El diseño modular permite personalizar fácilmente el mensaje, velocidad o tamaño.
