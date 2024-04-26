## Dependências
- pyserial
- esptool
- visual studio + pymark

## Instalação
para apagar todos os dados da placa, não queremos a biblioteca arduino

> [!NOTE]
> esptool.py --port /dev/ttyUSB0 erase_flash

para gravar os dados de firmware

> [!NOTE]
> esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect 0 diretori/firmware.bin
>