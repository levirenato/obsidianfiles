PWM (Pulse Width Modulation) significa modulação por largura de pulso, consiste em uma técnica que permite a partir de uma saída digital reproduzir uma série de valores entre o valor mínimo e o valor máximo, no *NodeMCU* essa série é representada pelos valores inteiros entre 0 e 1023. O PWM permite que um valor de uma porta possa ser um valor além de ligado ou desligado, esse recurso é utilizado por exemplo para controlar a rotação de um motor ou a intensidade de um led.

para usar o PWM é necessário importar dentro da biblioteca machine 

```
from machine import PWM
```