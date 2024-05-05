Um LED RGB (Red, Green, Blue) é um tipo especial de [[Led]] que é capaz de emitir luz em três cores primárias: vermelho, verde e azul. Ele contém três LEDs individuais em um único encapsulamento, cada um dos quais emite uma das cores primárias.

O funcionamento básico de um LED RGB é semelhante ao de um LED convencional, mas com a capacidade adicional de controlar a intensidade de cada cor separadamente. Isso é alcançado através de uma combinação de corrente elétrica e modulação de largura de pulso ([[PWM]], do inglês Pulse Width Modulation), onde a luz de cada cor é ajustada variando a quantidade de tempo que o LED permanece ligado em relação ao tempo total.

==**Um LED RGB pode ser anodo comum ou catodo comum==. Essa distinção se refere à forma como os terminais do LED são conectados dentro do encapsulamento em relação à tensão de alimentação.**

![[led_rgb.png]]
*Detalhes do led RGB*

Podemos criar um objeto utilizando POO:
```
from machine import Pin, PWM # type: ignore
from time import sleep
from random import getrandbits

class LedRGB():
    VERMELHO = 0
    VERDE = 1
    AZUL = 2
    CATODO_COMUM = 3
    ANODO_COMUM = 4
    
    def __init__(self, pinoVermelho, pinoVerde, pinoAzul, tipo=CATODO_COMUM):
        self.__tipo = tipo
        self.__pwm = []
        
        if self.__tipo == self.CATODO_COMUM:
            self.__pwm.append(PWM(Pin(pinoVermelho), freq=20000, duty=0))
            self.__pwm.append(PWM(Pin(pinoVerde), freq=20000, duty=0))
            self.__pwm.append(PWM(Pin(pinoAzul), freq=20000, duty=0))
        else:
            self.__pwm.append(PWM(Pin(pinoVermelho), freq=20000, duty=1023))
            self.__pwm.append(PWM(Pin(pinoVerde), freq=20000, duty=1023))
            self.__pwm.append(PWM(Pin(pinoAzul), freq=20000, duty=1023))
        
    def ligar(self, cor):
        if self.__tipo == self.CATODO_COMUM:
            self.__pwm[cor].duty(1023)
        if self.__tipo == self.ANODO_COMUM:
            self.__pwm[cor].duty(0)
    def desligar(self, cor):
        if self.__tipo == self.CATODO_COMUM:
            self.__pwm[cor].duty(0)
        if self.__tipo == self.ANODO_COMUM:
            self.__pwm[cor].duty(1023)
    
    def desligarTodas(self):
        for cor in range(3):
            self.desligar(cor)
    
    def intensidade(self, cor, valor):
        if self.__tipo == self.CATODO_COMUM:
            self.__pwm[cor].duty(valor)
        else:
            self.__pwm[cor].duty(1023 - valor)
        
    def caleidoscopio(self, transicao=0.1,duracao=1.0):
        for vezes in range(int(duracao/transicao) + 1):
            for cor in range(3):
                self.__pwm[cor].duty(getrandbits(10))
            sleep(transicao)
```