O Push button (botão de pressão) é uma chave que, quando pressionado o botão, ela abre ou fecha o circuito, convertendo assim, um comando mecânico em elétrico. Geralmente eles tem um contato de ação momentânea, abrindo ou fechando o circuito apenas de modo momentâneo.

![[pushbutton.png]]

Podemos criar um objeto utilizando POO:
```
from time  import sleep
from machine import Pin # type: ignore

class Botao():
    def __init__(self, pino):
        self.__botao = Pin(pino, Pin.IN)
        self.__estado = 0
        self.__aterior = 0
    def pressionado(self):
        return self.__botao.value()
    
    def estado(self, quantidade=2):
        if self.pressionado() or self.__aterior == 0:
            self.__estado = self.__estado + 1
            if self.__estado >= quantidade:
                self.__estado = 0
        self.__aterior = self.pressionado()
        sleep(0.1)
        return self.__estado
```
