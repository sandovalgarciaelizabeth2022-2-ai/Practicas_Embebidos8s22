Objetivo: Configurar y utilizar un sensor DHT11 para medir temperatura y humedad, graficar los datos
en tiempo real en una pantalla OLED y mostrar iconos representativos.

Instalación de Librerías
sudo apt update && sudo apt install -y python3-pip
pip3 install adafruit-circuitpython-dht adafruit-circuitpython-ssd1306 pillow
matplotlib
sudo apt install libgpiod2

Estructura del Proyecto

dht11-oled/
│
├── main.py
├── sensors/
│ └── dht11_reader.py
├── display/
│ ├── oled_display.py
│ └── icons/
│ ├── temp.png
│ └── humidity.png
└── utils/
└── data_logger.py

Código Modular
sensors/dht11_reader.py
import adafruit_dht
import board
import time
class DHT11Reader:
def __init__(self, pin=board.D17):
self.sensor = adafruit_dht.DHT11(pin)
def read(self):
try:
temp = self.sensor.temperature
hum = self.sensor.humidity
return temp, hum
except RuntimeError:
return None, None
display/oled_display.py
from PIL import Image, ImageDraw, ImageFont
import board, busio, adafruit_ssd1306

import time, os
class OLEDDisplay:
def __init__(self, width=128, height=64):
i2c = busio.I2C(board.SCL, board.SDA)
self.disp = adafruit_ssd1306.SSD1306_I2C(width, height, i2c)
self.width = width
self.height = height
self.disp.fill(0)
self.disp.show()
self.font = ImageFont.load_default()
self.load_icons()

def load_icons(self):
base = os.path.dirname(__file__)
self.temp_icon = Image.open(os.path.join(base, "icons",
"temp.png")).resize((16,16)).convert("1")
self.hum_icon = Image.open(os.path.join(base, "icons",
"humidity.png")).resize((16,16)).convert("1")
def update(self, temperature, humidity):
image = Image.new("1", (self.width, self.height))
draw = ImageDraw.Draw(image)
image.paste(self.temp_icon, (0, 0))
draw.text((20, 0), f"{temperature}°C", font=self.font, fill=255)
image.paste(self.hum_icon, (0, 20))
draw.text((20, 20), f"{humidity}%", font=self.font, fill=255)
self.disp.image(image)
self.disp.show()
main.py
import time
from sensors.dht11_reader import DHT11Reader
from display.oled_display import OLEDDisplay

def main():
sensor = DHT11Reader()
display = OLEDDisplay()
while True:
temp, hum = sensor.read()
if temp is not None and hum is not None:

display.update(temp, hum)
time.sleep(2)
if __name__ == "__main__":
main()






