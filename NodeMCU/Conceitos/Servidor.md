Como está em linguagem Python e tem uma placa WiFi integrada, é possível criar um servidor a partir do NodeMCU.

```
from mapping import ports
from time import sleep

import network # type: ignore
import usocket as socket # type: ignore
import gc

gc.collect()

html = """<!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Botão Numerado</title>
    </head>
    <body>
        <h1>Hello world!<h1/>
    </body>
    </html>
    """
estacao = network.WLAN(network.STA_IF)
estacao.active(True)
estacao.connect('nome_wifi','Senha')

while estacao.isconnected() == False:
    pass
print('Conexão realizada')
print(estacao.ifconfig())

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('',80))
s.listen(5)

try:
    while True:
        conexao, endereco = s.accept()
        print(f"Conexão de ",str(endereco))
        requisicao = conexao.recv(1024)
        requisicao = str(requisicao)
        conexao.send('HTTP/1.1 200 OK\n')
        conexao.send('Content-Type: text/html\n')
        conexao.send('Connection: close\n\n')
        conexao.sendall(html)
        conexao.close()
except KeyboardInterrupt:
    s.close()
    estacao.active(False)
```

vamos olhar com mais detalhes:

```
html = """<!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Botão Numerado</title>
    </head>
    <body>
        <h1>Hello world!<h1/>
    </body>
    </html>
    """
```
definimos a variável que irá conter a página HTML que sera enviada pelo servidor.

```
estacao = network.WLAN(network.STA_IF)
estacao.active(True)
estacao.connect('nome_wifi','Senha')

while estacao.isconnected() == False:
    pass
print('Conexão realizada')
print(estacao.ifconfig())
```
Neste bloco, vamos conectar ao [[WiFi]] conforme visto no módulo de [[WiFi]] 

```
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('',80))
s.listen(5)
```
Aqui criamos o soquete e realizamos a ligação do soquete a porta TCP 80, que é a porta padrão usada pelo protocolo HTTP. Em seguida colocamos associada do soquete em modo escuta, em que o parâmetro passado para o método listen, que nesse caso é 5, indica o máximo de solicitações simultâneas.

```
try:
    while True:
        conexao, endereco = s.accept()
        print(f"Conexão de ",str(endereco))
        requisicao = conexao.recv(1024)
        requisicao = str(requisicao)
        conexao.send('HTTP/1.1 200 OK\n')
        conexao.send('Content-Type: text/html\n')
        conexao.send('Connection: close\n\n')
        conexao.sendall(html)
        conexao.close()
except KeyboardInterrupt:
    s.close()
    estacao.active(False)
```
finalmente o programa entra em um laço em que aceita pedidos de conexão, processa a requisição e envia o conteúdo. O conteúdo é ***dividido em duas partes*** então primeiramente é enviado o cabeçalho e posteriormente o conteúdo HTML após isso a conexão com o cliente é encerrada, mas o soquete permanece  ativo aguardando novas solicitações.