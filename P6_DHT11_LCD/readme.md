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

