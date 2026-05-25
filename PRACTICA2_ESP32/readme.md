PRACTICA 2 ESP32 Desarrollo — Programa 2A: Ticker de Texto por Software
# P2A_ticker.py — Texto que se desplaza horizontalmente (ticker)
from machine import I2C, Pin
import ssd1306
import time
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)
mensaje = &#39; &gt;&gt;&gt; TESOEM - Sistemas Embebidos - ISC 2025 &lt;&lt;&lt; &#39;
pos_x = 128 # inicio fuera de pantalla por la derecha

TESOEM | Sistemas Embebidos Aplicados a Móviles | Manual Pantalla OLED SSD1306

MSCD. Gustavo Romero — TESOEM Pág. 16 / 31
while True:
oled.fill(0)
oled.text(mensaje, pos_x, 28, 1)
oled.show()
pos_x -= 3 # velocidad: 3 px por frame
# Reiniciar cuando el texto complete su recorrido
if pos_x &lt; -(len(mensaje) * 8):
pos_x = 128
time.sleep_ms(40) # ~25 fps

Desarrollo — Programa 2B: Scroll Vertical (Noticias)
# P2B_scroll_vertical.py — Lista de items que sube como créditos de película
from machine import I2C, Pin
import ssd1306
import time
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)

#13
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

mensaje = ' >>> TESOEM - EMBEBIDOS <<< '
pos_x = 128

while True:
    oled.fill(0)

    if 30 <= pos_x <= 70:
        oled.fill(1)
        oled.text(mensaje, pos_x, 28, 0)
    else:
        oled.text(mensaje, pos_x, 28, 1)

    oled.show()

    pos_x -= 3

    if pos_x < -(len(mensaje) * 8):
        pos_x = 128

    time.sleep_ms(40)

    #14
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

noticias = [
    'Temp: 23 C',
    'Humedad: 65%',
    'Presion: 1013mb',
    'Velocidad: 12km/h',
    'Calidad aire',
    'UV Index: 3',
]

ALTURA_TOTAL = len(noticias) * 10 + 64

while True:
    for offset in range(ALTURA_TOTAL):
        oled.fill(0)

        oled.fill_rect(0, 0, 128, 12, 1)
        oled.text('ESTACION METEO', 8, 2, 0)

        for i, texto in enumerate(noticias):
            y = i * 10 - offset + 64

            if 14 < y < 64:
                oled.text(texto, 2, y, 1)

        oled.show()
        time.sleep_ms(50)

        #15
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

ADDR = 0x3C

def cmd(c):
    i2c.writeto(ADDR, bytes([0x00, c]))

oled.fill(0)
oled.text('DIAGONAL', 28, 20, 1)
oled.text('SCROLL', 36, 35, 1)
oled.show()

time.sleep(1)

cmd(0x2E)

cmd(0x29)
cmd(0x00)
cmd(0x00)
cmd(0x00)
cmd(0x07)
cmd(0x01)

cmd(0x2F)

time.sleep(8)

cmd(0x2E)

oled.fill(0)
oled.text('Scroll OFF', 12, 28, 1)
oled.show()

#16
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

horas = 0
minutos = 0
segundos = 0

while True:
    oled.fill(0)

    if segundos % 2 == 0:
        reloj = '{:02d}:{:02d}:{:02d}'.format(horas, minutos, segundos)
    else:
        reloj = '{:02d} {:02d} {:02d}'.format(horas, minutos, segundos)

    oled.text('RELOJ DIGITAL', 12, 10, 1)
    oled.text(reloj, 28, 32, 1)

    oled.show()

    time.sleep(1)

    segundos += 1

    if segundos == 60:
        segundos = 0
        minutos += 1

    if minutos == 60:
        minutos = 0
        horas += 1

    if horas == 24:
        horas = 0
