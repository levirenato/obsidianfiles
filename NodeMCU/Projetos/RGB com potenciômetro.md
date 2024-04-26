## Resumo

controlando o [[Led RGB]] é necessário utilizar  a técnica [[PWM]] para oscilar a frequência e assim alterar a cor

```
# main.py -- put your code here!

from mapping import ports
from machine import Pin, PWM, ADC # type: ignore
from time import sleep

#led = Pin(ports.D3, Pin.OUT)

pot = ADC(0)

# RGB

Min = 1023

Max = 0

r = PWM(Pin(ports.D5), freq=20000, duty=Min)

g = PWM(Pin(ports.D6), freq=20000, duty=Min)

b = PWM(Pin(ports.D7), freq=20000, duty=Min)

  
  

while True:

	r.duty(pot.read() * 2)

	g.duty(pot.read())

	b.duty(pot.read() * 3)

	sleep(1.0)
```