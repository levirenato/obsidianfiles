Um pisca pisca simples que a cada 1 segundo o led pisca

```
from mapping import ports
from machine import Pin, PWM # type: ignore
from time import sleep

led = Pin(ports.D2, Pin.OUT)

while True:
	led.value(1)
	sleep(1)
	led.value(0)
	sleep(1)
```