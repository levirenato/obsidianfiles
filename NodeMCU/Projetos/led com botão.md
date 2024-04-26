
Faremos um [[Led]] ascender assim que pressionamos o botão, aprenderemos como funcionar um botão para este projeto pode-se usar qualquer tipo de LED.


```
from mapping import ports
from machine import Pin # type: ignore
from time import sleep


led = Pin(ports.D2, Pin.OUT)
botao = Pin(ports.D7, Pin.IN)

while True:
	led.value(botao.value())
	sleep(0.2)
```

Porém podemos notar que o botão só funcionar quando enquanto ficar pressionando, isso acontece porque estamos utilizando um [[Botão de pressão]] com isso ele só mantem o estado enquanto permanecer pressionado, para contornar isto precisamos salvar o estado anterior ou seja verificar se o botão foi pressionado e manter o LED ativado via software. e podemos fazer isto no código abaixo.

```
from mapping import ports
from machine import Pin, PWM # type: ignore
from time import sleep

led = Pin(ports.D2, Pin.OUT)
botao = Pin(ports.D7, Pin.IN)
estado = False

while True:
	if botao.value() == 1:
		estado = not estado
		led.value(estado)
		sleep(0.2)
		sleep(0.2)
```