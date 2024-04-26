para este projeto pode-se usar qualquer tipo de led, porém vou usar um led [[RGB]]



```
# main.py -- put your code here!

from mapping import ports
from machine import Pin, PWM # type: ignore
from time import sleep
from random import getrandbits

#led = Pin(ports.D3, Pin.OUT)

botao = Pin(ports.D2, Pin.IN)

  
# RGB

Min = 1023

Max = 0

r = PWM(Pin(ports.D5), freq=20000, duty=Min)

g = PWM(Pin(ports.D6), freq=20000, duty=Min)

b = PWM(Pin(ports.D7), freq=20000, duty=Min)

  
while True:

	if botao.value() == 1:

		r.duty(getrandbits(10))

		g.duty(getrandbits(10))

		b.duty(getrandbits(10))

		sleep(1.0)
```