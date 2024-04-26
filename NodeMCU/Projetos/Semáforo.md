texto

```
from mapping import ports
from machine import Pin, PWM # type: ignore
from time import sleep

  

led_vd = Pin(ports.D4, Pin.OUT)
led_am = Pin(ports.D3, Pin.OUT)
led_vm = Pin(ports.D2, Pin.OUT)

while True:
	led_vd.value(1)
	sleep(1)
	led_am.value(1)
	led_vd.value(0)
	sleep(1)
	led_vm.value(1)
	led_am.value(0)
	sleep(1)
	led_vm.value(0)
```

