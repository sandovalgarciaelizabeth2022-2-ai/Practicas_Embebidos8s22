PRACTICA 3 — Texto y Manejo de Colores / Inversión

Programa 3A — Inversión y alerta

python
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

def mostrar_info():
    oled.fill(0)
    oled.text('Sistema OK', 16, 20, 1)
    oled.text('T: 24.5 C', 20, 36, 1)
    oled.show()

def alerta(mensaje, repeticiones=5):

    for _ in range(repeticiones):

        oled.fill(1)
        oled.text('! ALERTA !', 16, 8, 0)
        oled.text(mensaje, 0, 28, 0)
        oled.show()

        time.sleep_ms(300)

        oled.fill(0)
        oled.text('! ALERTA !', 16, 8, 1)
        oled.text(mensaje, 0, 28, 1)
        oled.show()

        time.sleep_ms(300)

mostrar_info()

time.sleep(2)

alerta('TEMP ALTA!')

mostrar_info()


Programa 3B — Paneles con fondo

python
from machine import I2C, Pin
import ssd1306

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

oled.fill(0)

oled.fill_rect(0, 0, 128, 14, 1)
oled.text('TESOEM ISC', 16, 3, 0)

oled.line(0, 15, 127, 15, 1)

oled.text('CPU:', 0, 20, 1)
oled.text('78%', 40, 20, 1)

oled.text('RAM:', 0, 32, 1)
oled.text('45%', 40, 32, 1)

oled.rect(64, 20, 60, 8, 1)
oled.fill_rect(65, 21, int(58 * 0.78), 6, 1)

oled.rect(64, 32, 60, 8, 1)
oled.fill_rect(65, 33, int(58 * 0.45), 6, 1)

oled.fill_rect(0, 52, 128, 12, 1)
oled.text('v1.0 Activo', 12, 54, 0)

oled.show()


Programa 3C — Modo oscuro / claro

python
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

def dibujar_contenido(modo_oscuro):

    fondo = 0 if modo_oscuro else 1
    texto = 1 if modo_oscuro else 0

    oled.fill(fondo)

    oled.text('Modo:', 10, 10, texto)

    etiqueta = 'Oscuro' if modo_oscuro else 'Claro'

    oled.text(etiqueta, 10, 24, texto)

    oled.text('TESOEM 2025', 10, 40, texto)

    oled.show()

modo = True

while True:

    dibujar_contenido(modo)

    time.sleep(2)

    modo = not modo


Actividad 17 — Semáforo visual

python
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

def mostrar_estado(estado):

    oled.fill(0)

    oled.text('SEMAFORO', 28, 0, 1)

    if estado == 'VERDE':
        oled.text('VERDE', 44, 28, 1)

    elif estado == 'AMARILLO':
        oled.text('AMARILLO', 28, 28, 1)

    elif estado == 'ROJO':
        oled.text('ROJO', 48, 28, 1)

    oled.show()

while True:

    mostrar_estado('VERDE')
    time.sleep(2)

    mostrar_estado('AMARILLO')
    time.sleep(2)

    for _ in range(4):

        mostrar_estado('ROJO')
        time.sleep(0.5)

        oled.fill(0)
        oled.show()

        time.sleep(0.5)
