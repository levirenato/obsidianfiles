São componentes eletrônicos bastante comuns e acessíveis. Consistindo basicamente em [[Led]]s interligados por um pino comum. são utilizados para exibir principalmente informações numéricas e podem ser do tipo [[Cátodo]] ou [[Ânodo]] comuns.

**Se for um Ânodo comum ele é ligado na porta "3v3"**
**Se for um Cátodo comum ele é ligado na porta "GND"**

É necessário pesquisar os detalhes do componente no Google para obter a documentação dele no nosso caso o modelo é o 5161AS, por exemplo:
![[display_7_datasheet_1.png]]
*aqui temos um resumo do componente segundo seu datasheet*

podemos então reparar que este é um Cátodo comum.

também para o caso desse componente é importante acharmos a ordem dos pinos, pois eles tem uma ordem própria que não é aleatória.

![[display_7_datasheet_2.png]]
*Diagrama da ordem dos LEDs*

Para o nosso exemplo a disposição física dos pinos coma as portas é:
![[display_7_datasheet_3.png]]
*Diagrama real*

Então criaremos um objeto através de POO, para controlar melhor o dispositivos:

```
from machine import Pin # type: ignore

class Display7Segmentos():

	CATODO_COMUM = 0
	ANODO_COMUM = 1
	
	def __init__(self, pinos, ponto,tipo=CATODO_COMUM):

		self.__hex = [
		[1, 1, 1, 1, 1, 1, 0], # 0
		[0, 1, 1, 0, 0, 0, 0], # 1
		[1, 1, 0, 1, 1, 0, 1], # 2
		[1, 1, 1, 1, 0, 0, 1], # 3
		[0, 1, 1, 0, 0, 1, 1], # 4
		[1, 0, 1, 1, 0, 1, 1], # 5
		[1, 0, 1, 1, 1, 1, 1], # 6
		[1, 1, 1, 0, 0, 0, 0], # 7
		[1, 1, 1, 1, 1, 1, 1], # 8
		[1, 1, 1, 1, 0, 1, 1], # 9
		]
		
		self.__tipo = tipo
		self.__segmentos = []
		for p in pinos:
		self.__segmentos.append(Pin(p, Pin.OUT))
		self.__ponto = Pin(ponto, Pin.OUT)
		self.ponto(False)
	
	def exibir(self, digito):
		for i in range(len(self.__segmentos)):
			self.__segmentos[i].value(abs(self.__hex[digito][i] - self.__tipo))
	
	def apagar(self):
		for seg in self.__segmentos:
		seg.value(self.__tipo)
	
	def ponto(self,ligado):
		self.__ponto.value(abs(ligado - self.__tipo))
```
