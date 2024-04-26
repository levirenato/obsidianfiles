
Um programa simples que pisca o [[Led]] integrado a placa.

```
from time import sleep
from machine import Pin

led = Pin(2, Pin.OUT)

while True:
	led.value(1)
	sleep(0.5)
	led.valeu(0)
	sleep(0.5)
```
