Nesse projeto vamos obter o valor do [[Termístor]] conectado ao [[ADC]], converter para Celsius e exibir no shell.


```
from mapping import ports
from machine import Pin, ADC # type: ignore
from time import sleep
from math import log

def obterTemperatura(termistor):
	tempK = log(10000.0 * (1024.0/ termistor - 1))
	tempk = 1/(0.001129148 + (0.000234125 + (0.0000000876741 * tempK * tempK)) * tempk)
	tempC = tempk - 273.15
	return tempC


led_vd = Pin(ports.D4, Pin.OUT)
led_am = Pin(ports.D3, Pin.OUT)
led_vm = Pin(ports.D2, Pin.OUT)

term = ADC(0)

while True:

	temp = obterTemperatura(term.read())
	print(f"Temperatura: {round(temp, 1)}°C")
	sleep(0.1)
```