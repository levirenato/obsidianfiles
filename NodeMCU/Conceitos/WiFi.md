Uma grande característica do NodeMCU é possuir wi-fi integrado, através da **biblioteca network**.

```
import network # Importa biblioteca

wifi = network.WLAN(network.AP_IF) # Cria a instancia Wifi

wifi.active(True) # Ativa a instacia

wifi.scan() # Escaneia todos os dipositivos disponivéis

wifi.connect('nome_wifi','senha')

```
