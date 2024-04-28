nesse projeto vamos usar um [[LDR]] conectado ao [[ADC]] e com base na quantidade de luz recebida pelo sensor, o programa vai determinar se vai acender um, dois ou três [[Led]]'s

```
from mapping import ports
from machine import Pin, PWM, ADC # type: ignore
from time import sleep
  
def mapear(x, in_min, in_max, out_min, out_max):

return (x - in_min) * (out_max - out_min) / (in_max - in_min) + out_min

led_vd = Pin(ports.D4, Pin.OUT)
led_am = Pin(ports.D3, Pin.OUT)
led_vm = Pin(ports.D2, Pin.OUT)
  
ldr = ADC(0)
  
while True:

	valor_ldr = ldr.read()
	
	valor = int(mapear(valor_ldr, 0, 1024, 0, 4))

	if valor < 1:
		led_vd.value(0)
		led_am.value(0)
		led_vm.value(0)
		print(f"ADC: {valor_ldr} / Mapear: {valor}")
	
	elif valor == 1:
		led_vd.value(1)
		led_am.value(0)
		led_vm.value(0)
		print(f"ADC: {valor_ldr} / Mapear: {valor}")
	
	elif valor == 2:
		led_vd.value(1)
		led_am.value(1)
		led_vm.value(0)
		print(f"ADC: {valor_ldr} / Mapear: {valor}")

	elif valor == 3:
		led_vd.value(1)
		led_am.value(1)
		led_vm.value(1)
		print(f"ADC: {valor_ldr} / Mapear: {valor}")

	sleep(0.1)
```
