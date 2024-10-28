---
tags:
  - RabbitMQ
  - DevOps
---
RabbitMQ é um servidor de troca de mensagens de código aberto que processa mensagens entre **produtores** e **consumidores**, funcionando como um intermediário. Ele é utilizado em comunicação entre sistemas e é compatível com diversas linguagens de programação.

Ele opera de forma Assíncrona e possui sistemas de filas com várias opções de encaminhamento, ele lida com o tráfego de mensagens bem rápido.

A porta padrão com RabbitMQ é 15672, a senha  padrão é guest e usuário guest.

# Funcionamento

## Filas

O RabbitMQ funciona através de sistemas de filas, a mensagem é enviada pelo **produtor** para uma fila e o **consumidor** recebe a mensagem na ordem da fila.

![[Pasted image 20241027095510.png]] 

# Laboratorio I
Obs: Antes de mais nada é necessário salientar que é preciso da biblioteca python "pika" para trabalhar  com o rabbitmq.
Ex 1:
sender.py
```import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()

channel.queue_declare("hello world!")

mensages = {
    "Inglês": "Hello, World!",
    "Português": "Olá, Mundo!",
    "Espanhol": "¡Hola, Mundo!",
    "Francês": "Bonjour, le monde!",
    "Alemão": "Hallo, Welt!",
    "Italiano": "Ciao, Mondo!",
}

for mensage in mensages:
    channel.basic_publish(
        exchange="", routing_key="hello world!", body=str(mensages.get(mensage))
    )
    print(f"Mensagem '{mensages.get(mensage)}' enviada em {mensage}")
    time.sleep(1.5)

connection.close()
```

consumer.py
```import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()

channel.queue_declare("hello world!")


def callback(ch, method, properties, body: bytes):
    print(f"body : {body.decode()}")


channel.basic_consume(queue="hello world!", auto_ack=True, on_message_callback=callback)

print(" [*] waiting messages (ctrl + c to cancel)")

channel.start_consuming()
```

Saida:
![[Pasted image 20241027155209.png]]
Se existir **dois consumidores** na mesma fila ele vai alternar por padrão, seguindo no consumidor de cima e depois o de baixo.
ex: 
	produtor: manda mensagem [mensagem I, mensagem II]
	consumidor 1: recebe mensagem 1
	consumidor II: recebe mensagem II

![[Pasted image 20241027105548.png]]

Ao executar outra vez o mesmo script você cria outro consumidor e segue o comportamento padrão do exchange, você conseguir observar pelo callback ele enviando primeiro para o de cima de depois para o de baixo:
![[Pasted image 20241027155412.png]]

### ACK (Acknowledge)

- O **acknowledge** é uma função que informa ao produtor que a mensagem foi recebida.
- Se não houver ack, a mensagem permanece na fila. Uma vez reconhecida pelo consumidor, ela pode ser removida.

## Exchange
O exchange é  a estrutura de dados que mandamos a informação antes dela seguir para fila, ela que vai decidir para qual fila e como vai funcionar o envio das mensagens.

- ### Fanout
fanout é o modo exchange para enviar para filas simultaneamente, por padrão o envio é alternado entre os consumidores que estão na mesma fila, mas nesse modo a mensagem é enviada igualmente para os dois.
![[Pasted image 20241027154335.png]]

vamos fazer as devidas alterações no script para vermos ele no modo fanout.

sender.py
```import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()


channel.exchange_declare(exchange="FANOUT", exchange_type="fanout")

mensages = {
    "Inglês": "Hello, World!",
    "Português": "Olá, Mundo!",
    "Espanhol": "¡Hola, Mundo!",
    "Francês": "Bonjour, le monde!",
    "Alemão": "Hallo, Welt!",
    "Italiano": "Ciao, Mondo!",
}

for mensage in mensages:
    channel.basic_publish(
        exchange="FANOUT", routing_key="", body=str(mensages.get(mensage))
    )
    print(f"Mensagem '{mensages.get(mensage)}' enviada em {mensage}")
    time.sleep(1.5)

connection.close()
```

consumer.py
```import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()

channel.exchange_declare(exchange="FANOUT", exchange_type="fanout")

queue = channel.queue_declare("", exclusive=True)

queue_name = queue.method.queue

channel.queue_bind(queue=queue_name, exchange="FANOUT")


def callback(ch, method, properties, body: bytes):
    print(f"body : {body.decode()}")


channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)

print(" [*] waiting messages (ctrl + c to cancel)")

channel.start_consuming()
```

Saída:
![[Pasted image 20241027160454.png]]
Se você observar está enviando para os dois consumidores ao mesmo tempo.

- ### DIRECT
Nesse modo o exchange decide para quem mandar a mensagem de acordo com o routing_key(fila), se não existir ele não envia.

![[Pasted image 20241027160706.png]]

Aqui por exemplo temos uma rota de log que vai mandar a mensagem de acordo com a mensagem para determinada fila.

sender.py:
```
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()


channel.exchange_declare(exchange="logs", exchange_type="direct")

mensages = [
    ("sucess", "Mensagem de sucesso 1"),
    ("sucess", "Mensagem de sucesso 2"),
    ("warining", "Mensagem de waring 1"),
    ("warining", "Mensagem de waring 2"),
    ("info", "Mensagem de info 1"),
    ("info", "Mensagem de info 2"),
]

for mensage in mensages:
    channel.basic_publish(exchange="logs", routing_key=mensage[0], body=mensage[1])
    print(f"type: '{mensage[0]}' body: {mensage[1]}")
    time.sleep(1.5)

connection.close()
```

consumer.py {arg} 

obs tem que passar no arg o tipo de mensagem que quer receber.
```
import pika
import sys


def callback(ch, method, properties, body: bytes):
    print(f"body : {body.decode()}")


connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()

channel.exchange_declare(exchange="logs", exchange_type="direct")

queue = channel.queue_declare("", exclusive=True)

queue_name = queue.method.queue

serverities = sys.argv[1:]

if not serverities:
    sys.stderr.write("Usage: %s [info] [warining] [error]\n" % sys.argv[0][0])
    sys.exit(1)

for severity in serverities:
    channel.queue_bind(queue=queue_name, exchange="logs", routing_key=severity)

print(" [*] waiting messages (ctrl + c to cancel)")

channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)


channel.start_consuming()
```

Saída:
![[Pasted image 20241027164253.png]]

- ### Topic
Neste exchange é possível adicionar um wildcard, para facilitar o tipo de envio que a gente quer fazer.
Por exemplo: `*.error` ou `A.*`  veja no exemplo abaixo:
![[Pasted image 20241027170752.png]]

sender.py:
```
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()


channel.exchange_declare(exchange="logs_topic", exchange_type="topic")

mensages = ["Primeiro log", "segundo log", "terceiro log", "quarto log", "quinto log"]
severities = ["info", "error", "warining", "error", "info"]
components = ["A", "B", "A", "A", "B"]

for i in range(0, 5):
    routing_key = components[i] + "." + severities[i]

    channel.basic_publish(
        exchange="logs_topic", routing_key=routing_key, body=mensages[i]
    )
    print(f"type: '{routing_key}' body: {mensages[i]}")
    time.sleep(1.5)

connection.close()
```

consumer.py

```
import pika
import sys


def callback(ch, method, properties, body: bytes):
    print(f"body : {body.decode()}")


connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

channel = connection.channel()

channel.exchange_declare(exchange="logs_topic", exchange_type="topic")

queue = channel.queue_declare("", exclusive=True)

queue_name = queue.method.queue

serverities = sys.argv[1:]

if not serverities:
    sys.stderr.write("Usage: %s [biding_key]...\n" % sys.argv[0])
    sys.exit(1)

for severity in serverities:
    channel.queue_bind(queue=queue_name, exchange="logs_topic", routing_key=severity)

print(" [*] waiting messages (ctrl + c to cancel)")

channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)


channel.start_consuming()
```

Saída:
![[Pasted image 20241027170854.png]]


### Header Exchange

#### Producer (Sender)

```
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()
channel.exchange_declare(exchange="headers_exchange", exchange_type="headers")

mensages = [
    {"message": "Mensagem A", "headers": {"format": "pdf", "x-match": "all"}},
    {"message": "Mensagem B", "headers": {"format": "json", "x-match": "all"}},
    {"message": "Mensagem C", "headers": {"format": "xml", "x-match": "all"}},
]

for msg in mensages:
    properties = pika.BasicProperties(headers=msg["headers"])
    channel.basic_publish(
        exchange="headers_exchange", routing_key="", body=msg["message"], properties=properties
    )
    print(f"Mensagem '{msg['message']}' enviada com headers: {msg['headers']}")
    time.sleep(1.5)

connection.close()
```

#### Consumer (Receiver)

```
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()
channel.exchange_declare(exchange="headers_exchange", exchange_type="headers")

queue = channel.queue_declare("", exclusive=True)
queue_name = queue.method.queue

# Bind queue to the headers exchange with specific headers
headers = {"format": "pdf", "x-match": "all"}
channel.queue_bind(
    exchange="headers_exchange", queue=queue_name, arguments=headers
)

def callback(ch, method, properties, body):
    print(f"Recebido: {body.decode()}")

channel.basic_consume(queue=queue_name, on_message_callback=callback, auto_ack=True)
print("Esperando mensagens. Pressione CTRL+C para sair.")
channel.start_consuming()
```

Saída:
![[Pasted image 20241027174025.png]]
### Explicação

- **Header Exchange**: Utiliza cabeçalhos para roteamento de mensagens, permitindo grande flexibilidade.
    
- **Headers de Mensagem**: Definidos pelo produtor ao enviar uma mensagem.
    
- **x-match**: Definido como `"all"` ou `"any"`, onde `"all"` exige que todos os cabeçalhos coincidam e `"any"` permite que qualquer um dos cabeçalhos coincida.


## Visão geral

### Exchanges e Seus Tipos

#### O Que São Exchanges?

Exchanges são componentes fundamentais do RabbitMQ que roteiam mensagens do produtor para as filas. Eles atuam como "hub" que decide para qual fila uma mensagem deve ser direcionada, baseada em regras de roteamento que você define.

#### Tipos de Exchanges

1. **Direct Exchange**:
    
    - Usa chaves de roteamento específicas.
        
    - Uma mensagem é enviada para uma fila cuja chave de roteamento (routing_key) corresponde exatamente à chave especificada na mensagem.
        
    - Use quando você precisa de um roteamento específico e direto das mensagens.
        
2. **Fanout Exchange**:
    
    - Distribui mensagens para todas as filas vinculadas a ele, independentemente da chave de roteamento.
    - Útil para broadcast de mensagens, onde todas as filas devem receber a mesma mensagem.
        
3. **Topic Exchange**:
    
    - Usa padrões de chave de roteamento com curingas(wildcards).
    - Permite maior flexibilidade ao rotear mensagens para múltiplas filas, baseado em padrões como "log._" ou "_.error".
    - Use quando você precisa de um roteamento dinâmico e baseado em padrões.
        
4. **Headers Exchange**:
    
    - Roteia mensagens com base nos cabeçalhos especificados na mensagem, em vez de chaves de roteamento.
    - Proporciona grande flexibilidade usando headers (cabeçalhos) e x-match (all ou any).
        

### Queue vs Queue Key

- **Queue**:
    
    - É uma fila onde as mensagens são armazenadas até que sejam consumidas.
    - Consumidores se conectam a filas específicas para receber mensagens.
    - Filas podem ter múltiplos consumidores, e as mensagens são distribuídas entre eles.
        
- **Queue Key (Routing Key)**:
    
    - É uma chave usada pelos exchanges (exceto fanout) para determinar como rotear as mensagens para as filas.
    - Para direct exchange, a routing key deve corresponder exatamente ao nome da fila.
    - Para topic exchange, a routing key pode usar padrões com curingas para definir múltiplos destinos.
        

### Quando Usar Cada Tipo de Exchange

- **Direct Exchange**: Quando você quer enviar mensagens para filas específicas e precisa de um mapeamento direto e claro.
    
- **Fanout Exchange**: Quando você quer que todas as filas vinculadas a este exchange recebam cada mensagem, sem se importar com chaves de roteamento.
    
- **Topic Exchange**: Quando você precisa de um roteamento flexível com padrões complexos.
    
- **Headers Exchange**: Quando você quer rotear mensagens com base em metadados complexos, utilizando cabeçalhos.