Um LED (Light Emitting Diode), ou Diodo Emissor de Luz em português, é um componente eletrônico que emite luz quando uma corrente elétrica passa por ele. Ele é construído com materiais semicondutores que emitem fótons quando excitados pela corrente elétrica.

![[led.png]]
*led de diversas cores*

Pode-se observar que existem pernas de dois tamanhos diferentes, a menor é o [[Cátodo]](negativo) e a maior é o [[Ânodo]] (positivo)

![[led_details.png]]
*detalhes do circuito de um led*

Podemos criar um objeto utilizando POO:
```
from time import sleep
from machine import Pin, PWM # type: ignore

class Led():
    def __init__(self, pino):
        self._pwm = PWM(Pin(pino), freq=20000, duty=0)
        self._estado = False
        
    def ligar(self):
        self._pwm.duty(1023)
        
    def desligar(self):
        self._pwm.duty(0)
        
    def alternar(self, tempo=0.5):
        if self._estado:
            self.ligar()
        else:
            self.desligar()
        
        self._estado = not self._estado
        sleep(tempo)
        
    def piscar(self, tempo=0.5, vezes=1):
        for i in range(vezes * 2 + 1):
            self.alternar(tempo)
    
    def intensidade(self, valor):
        self._pwm.duty(valor)
```