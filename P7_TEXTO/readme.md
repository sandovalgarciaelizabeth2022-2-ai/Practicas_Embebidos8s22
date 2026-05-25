#Objetivo

Configurar y utilizar una pantalla LCD 2x16 con interfaz I2C en una Raspberry Pi para mostrar texto estático y dinámico (desplazable) usando Python bajo un enfoque modular.

#Materiales

Raspberry Pi (cualquier modelo con GPIO)

Pantalla LCD 16x2 con módulo I2C integrado

Cables puente (4 unidades)

Protoboard (opcional)

Fuente de alimentación para la Raspberry Pi

#Descripción

En esta práctica se controla una pantalla LCD de 2 líneas por 16 columnas utilizando el protocolo de comunicación I2C a través de las librerías RPLCD y smbus2.
El sistema funciona de forma modular:

lcd_utils.py: Contiene la lógica del controlador, permitiendo inicializar la pantalla en la dirección hexadecimal 0x27, borrar el display, escribir texto fijo ajustado a las dimensiones físicas y calcular un efecto de marquesina (scrolling) mediante subcadenas indexadas de texto.

main.py: Orquesta la inicialización, escribe un mensaje estático en la línea superior y despliega el texto dinámico en la línea inferior, asegurando la correcta liberación de los pines mediante un bloque de excepciones.

#Código
# lcd_utils.py
from RPLCD.i2c import CharLCD
import time

class LCDController:
    def __init__(self, address=0x27, cols=16, rows=2):
        self.lcd = CharLCD(i2c_expander='PCF8574', address=address,
                           port=1, cols=cols, rows=rows, charmap='A00',
                           auto_linebreaks=True)
        self.cols = cols
        self.rows = rows

    def clear(self):
        self.lcd.clear()

    def write(self, text, line=0):
        self.lcd.cursor_pos = (line, 0)
        self.lcd.write_string(text.ljust(self.cols))

    def scroll_text(self, text, line=0, delay=0.3, repeat=1):
        scroll_text = text + " " * self.cols
        for _ in range(repeat):
            for i in range(len(scroll_text) - self.cols + 1):
                self.write(scroll_text[i:i+self.cols], line)
                time.sleep(delay)

    def close(self):
        self.lcd.close(clear=True)
        # main.py
from lcd_utils import LCDController

def main():
    lcd = LCDController()
    try:
        lcd.clear()
        lcd.write("LCD Inicializado", line=0)
        lcd.scroll_text("Este texto se desplaza hacia la izquierda!", line=1,
                        delay=0.2, repeat=3)
    except Exception as e:
        print(f"Error: {e}")
    finally:
        lcd.close()

if __name__ == "__main__":
    main()
    
