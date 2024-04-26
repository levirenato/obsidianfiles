o modelo que uso é um wemos D1 genérico, que por sua vez tem uma placa asp8266 instalado

ele utiliza o firmware: https://micropython.org/download/ESP8266_GENERIC/

e tem um pequeno problema no mapeamento das porta, que é bastante confuso  então mapeei manualmente cada aporta ficando da seguinte forma:


para facilitar criei um modulo python chamado ports com a configuração das porta


- LED_BOARD = 2
- D0 = 3
- D1 = 1
- D2 = 16
- D3 = 5
- D4 = 4
- D5 = 14
- D6 = 12
- D7 = 13
- D8 = 0
- D10 = 15