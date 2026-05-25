Desarrollo — Programa 1A: Texto Estático Básico
# P1A_texto_basico.py — Texto estático en múltiples líneas
from machine import I2C, Pin
import ssd1306
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)
oled.fill(0)
# Línea 1 — título centrado (16 chars × 8px = 128px; cadena de 7 chars → inicio en
(4.5*8)=36)
oled.text(&#39;TESOEM&#39;, 36, 0, 1)
# Separador horizontal
oled.line(0, 10, 127, 10, 1)
# Información en filas
oled.text(&#39;Materia:&#39;, 0, 16, 1)
oled.text(&#39;Embebidos&#39;, 0, 26, 1)
oled.text(&#39;Alumno:&#39;, 0, 38, 1)
oled.text(&#39;Tu Nombre&#39;, 0, 48, 1)

TESOEM | Sistemas Embebidos Aplicados a Móviles | Manual Pantalla OLED SSD1306

MSCD. Gustavo Romero — TESOEM Pág. 13 / 31
oled.show()

Desarrollo — Programa 1B: Contador en Tiempo Real
# P1B_contador.py — Actualiza texto dinámicamente (simula sensor)
from machine import I2C, Pin
import ssd1306
import time
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)
contador = 0
while True:
oled.fill(0)
oled.text(&#39;Contador:&#39;, 0, 0, 1)
oled.text(str(contador), 40, 28, 1)
oled.text(&#39;Presiona RST&#39;, 0, 50, 1)
oled.show()
contador += 1
time.sleep(0.5)

Desarrollo — Programa 1C: Control de Contraste
# P1C_contraste.py — Cicla el brillo de la pantalla
from machine import I2C, Pin
import ssd1306
import time
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)
oled.fill(0)
oled.text(&#39;Contraste:&#39;, 16, 20, 1)
oled.show()
# Barrido de brillo: 0 (mínimo) → 255 (máximo) → 0
while True:
for nivel in range(0, 256, 5):
oled.contrast(nivel)
oled.fill(0)
oled.text(&#39;Brillo:&#39;, 16, 20, 1)
oled.text(str(nivel), 52, 36, 1)
oled.show()
time.sleep_ms(30)
for nivel in range(255, -1, -5):
oled.contrast(nivel)
oled.fill(0)
oled.text(&#39;Brillo:&#39;, 16, 20, 1)
oled.text(str(nivel), 52, 36, 1)
oled.show()
time.sleep_ms(30)

Actividades de la Práctica 1
9. Modifica P1A para que muestre tu nombre, número de control y carrera en tres líneas separadas.
10. 10. En P1B, agrega una barra de progreso que muestre el contador como porcentaje entre 0 y 100.
11. Experimenta con distintos valores de contraste y documenta visualmente (foto) los tres extremos:
contraste=0, contraste=128, contraste=255.
12. RETO: Implementa un temporizador de cuenta regresiva de 60 segundos con texto grande centrado.

#9
from machine import I2C, Pin
import ssd1306

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

oled.fill(0)

oled.text('NOMBRE', 0, 0, 1)
oled.text('No: 0000000', 0, 20, 1)
oled.text('Carrera: ISC', 0, 40, 1)

oled.show()

#10
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

contador = 0

while True:
    porcentaje = contador % 101
    barra = int(porcentaje * 100 / 100)

    oled.fill(0)
    oled.text('Contador:', 0, 0, 1)
    oled.text(str(porcentaje) + '%', 45, 18, 1)

    oled.rect(10, 40, 104, 10, 1)
    oled.fill_rect(12, 42, barra, 6, 1)

    oled.show()

    contador += 1
    time.sleep(0.1)

    #11
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

for nivel in [0, 128, 255]:
    oled.contrast(nivel)
    oled.fill(0)
    oled.text('Contraste:', 10, 20, 1)
    oled.text(str(nivel), 50, 40, 1)
    oled.show()
    time.sleep(5)
# 12
from machine import I2C, Pin
import ssd1306
import time

i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

for segundos in range(60, -1, -1):
    oled.fill(0)

    oled.text('Cuenta regresiva', 0, 0, 1)
    oled.text('Tiempo:', 35, 20, 1)
    oled.text(str(segundos), 55, 40, 1)

    oled.show()
    time.sleep(1)

oled.fill(0)
oled.text('FIN', 52, 28, 1)
oled.show()
