vamos precisar de:
1. [[Led]]
2. resistor

Vamos ligar e desligar um led simples, utilizando o [[Servidor]] criado pelo modulo de [[WiFi]] do microcontrolador.


para isso vamos precisar utilizar dois frameworks o jquery e o bootstrap

1. primeiro criaremos o documento HTML
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>App Iot</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.4.1/dist/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.4.1/dist/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>
</head>
<script>
    $(document).ready(function() {
        $("#on").click(function() {
            $.ajax({url: "/led/1", success: function(result) {
                $('#on').removeClass('btn-success').addClass('btn-default');
                $('#off').removeClass('btn-default').addClass('btn-danger');
            }})
        })
        $("#off").click(function() {
            $.ajax({url: "/led/0", success: function(result) {
                $('#on').removeClass('btn-default').addClass('btn-success');
                $('#off').removeClass('btn-danger').addClass('btn-default');
            }})
        })
    })
</script>
<body>
    <div class="container">
        <h1>NodeMCU</h1>
        <h4>Controle de LED</h4><br />
        <div class="btn-group">
            <button id="on" type="button" class="btn btn-success btn-lg">Acender</button><br /><br />
            <button id="off" type="button" class="btn btn-default btn-lg">Apagar</button>
        </div>
    </div>
</body>
</html>
```

2. Agora vamos no script adaptar as mudanças, criaremos uma função que busca o arquivo html.
```
from mapping import ports
from devices import led
from time import sleep

import network # type: ignore
import usocket as socket # type: ignore
import gc
gc.collect()

LED = led.Led(ports.D3)

def obter_arquivo(arquivo):
    conteudo = ''
    a= open(arquivo,'rb')
    conteudo = a.read()
    a.close()
    return conteudo

estacao = network.WLAN(network.STA_IF)
estacao.active(True)
estacao.connect('nome_wifi','senha')

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
        
        if requisicao.find('GET /led/1') != -1:
            LED.ligar()
        elif requisicao.find('GET /led/0') != -1:
            LED.desligar()
        
        html = obter_arquivo('app/index.html')
        conexao.send('HTTP/1.1 200 OK\n')
        conexao.send('Content-Type: text/html\n')
        conexao.send('Connection: close\n\n')
        conexao.sendall(html)
        conexao.close()
except KeyboardInterrupt:
    s.close()
    estacao.active(False)
```