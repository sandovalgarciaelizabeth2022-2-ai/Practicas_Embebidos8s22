PRACTICA 4 — Graficos Simples y Carga de Imagenes

Programa 4A — Primitivas graficas

python id="74krk4"
from machine import I2C, Pin
import ssd1306
import time
import math

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

def demo_lineas():

    oled.fill(0)

    oled.text('Lineas', 40, 0, 1)

    for i in range(0, 128, 16):
        oled.line(0, 64, i, 10, 1)

    oled.show()

    time.sleep(2)

def demo_rectangulos():

    oled.fill(0)

    oled.text('Rectang.', 28, 0, 1)

    oled.rect(5, 12, 50, 40, 1)

    oled.fill_rect(70, 12, 50, 40, 1)

    oled.text('OK', 82, 28, 0)

    oled.show()

    time.sleep(2)

def demo_elipses():

    oled.fill(0)

    oled.text('Elipses', 32, 0, 1)

    oled.ellipse(32, 40, 28, 20, 1)

    oled.ellipse(96, 40, 28, 20, 1, True)

    oled.show()

    time.sleep(2)

def demo_circulo_manual(cx, cy, r):

    x, y = r, 0

    while x >= y:

        puntos = [
            (cx+x, cy+y),
            (cx-x, cy+y),
            (cx+x, cy-y),
            (cx-x, cy-y),
            (cx+y, cy+x),
            (cx-y, cy+x),
            (cx+y, cy-x),
            (cx-y, cy-x)
        ]

        for px, py in puntos:
            oled.pixel(px, py, 1)

        y += 1

        x = int((r*r - y*y)**0.5)

def demo_circulos():

    oled.fill(0)

    oled.text('Circulos', 28, 0, 1)

    for radio in range(5, 30, 5):
        demo_circulo_manual(64, 38, radio)

    oled.show()

    time.sleep(2)

demo_lineas()
demo_rectangulos()
demo_elipses()
demo_circulos()


Programa 4B — Dashboard grafico

python id="9p3yyd"
from machine import I2C, Pin
import ssd1306
import time
import random

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

def barra(x, y, w, h, porcentaje, label):

    oled.text(label, x, y, 1)

    oled.rect(x, y+10, w, h, 1)

    relleno = int(w * porcentaje / 100)

    if relleno > 0:
        oled.fill_rect(x+1, y+11, relleno-1, h-2, 1)

    oled.text(str(porcentaje)+'%', x+w+2, y+10, 1)

for _ in range(20):

    temp = random.randint(18, 40)
    hum = random.randint(30, 90)
    cpu = random.randint(10, 95)

    oled.fill(0)

    oled.fill_rect(0, 0, 128, 11, 1)

    oled.text('DASHBOARD', 24, 2, 0)

    barra(0, 14, 90, 10, temp, 'T')
    barra(0, 30, 90, 10, hum, 'H')
    barra(0, 46, 90, 10, cpu, 'C')

    oled.show()

    time.sleep(1)


Programa 4C — Imagen bitmap

python id="d18do5"
from machine import I2C, Pin
import ssd1306
import framebuf

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)

oled = ssd1306.SSD1306_I2C(128, 64, i2c)

logo_bytes = bytearray([
    0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
    0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
    0xFF,0xFE,0x7F,0xFF,0xFF,0xF8,0x1F,0xFF,
    0xFF,0xF8,0x1F,0xFF,0xFF,0xE0,0x07,0xFF,
    0xFF,0xE0,0x07,0xFF,0xFF,0xE0,0x07,0xFF,
    0xFF,0xE0,0x07,0xFF,0xFF,0x80,0x01,0xFF,
    0xFF,0x00,0x60,0xFF,0xFF,0x00,0x60,0xFF,
    0xFF,0x00,0x38,0xFF,0xFE,0x00,0x18,0xFF,
    0xFC,0x00,0x18,0x3F,0xFC,0x00,0x18,0x3F,
    0xFC,0x00,0x00,0x3F,0xFC,0x00,0x00,0x3F,
    0xFC,0x00,0x00,0x3F,0xFC,0x00,0x00,0x3F,
    0xFC,0x00,0x00,0x3F,0xFE,0x00,0x00,0x7F,
    0xFF,0x00,0x00,0xFF,0xFF,0x80,0x01,0xFF,
    0xFF,0xC0,0x03,0xFF,0xFF,0xF8,0x1F,0xFF,
    0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
    0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
])

logo_fb = framebuf.FrameBuffer(
    logo_bytes,
    32,
    32,
    framebuf.MONO_HLSB
)

oled.fill(0)

oled.blit(logo_fb, 48, 16)

oled.show()


Actividad 26 — Osciloscopio

python id="c4g9kk"
from machine import I2C, Pin
import ssd1306
import math
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

fase = 0

while True:

    oled.fill(0)

    oled.text('OSCILOSCOPIO', 8, 0, 1)

    for x in range(128):

        y = int(48 + 12 * math.sin((x + fase) * 0.1))

        oled.pixel(x, y, 1)

    oled.show()

    fase += 2

    time.sleep_ms(50)


Actividad 28 — Ecualizador

python id="jlwm3t"
from machine import I2C, Pin
import ssd1306
import random
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

while True:

    oled.fill(0)

    for i in range(8):

        altura = random.randint(5, 50)

        x = i * 15 + 5

        oled.fill_rect(x, 63-altura, 10, altura, 1)

    oled.show()

    time.sleep_ms(120)


Actividad 29 — Pong

python id="2jqok0"
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

ball_x = 64
ball_y = 32

vx = 2
vy = 2

while True:

    oled.fill(0)

    oled.fill_rect(2, 20, 4, 20, 1)

    oled.fill_rect(122, 20, 4, 20, 1)

    oled.fill_rect(ball_x, ball_y, 3, 3, 1)

    ball_x += vx
    ball_y += vy

    if ball_y <= 0 or ball_y >= 61:
        vy *= -1

    if ball_x <= 6 or ball_x >= 119:
        vx *= -1

    oled.show()

    time.sleep_ms(20)
