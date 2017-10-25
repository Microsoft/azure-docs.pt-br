---
title: "Usar um dispositivo físico o Azure IoT Edge | Microsoft Docs"
description: "Como usar um dispositivo Texas Instruments SensorTag para enviar dados para um Hub IoT por meio de um gateway IoT Edge que executa em um dispositivo Raspberry Pi 3. O gateway é criado usando o Azure IoT Edge."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: andbuc
ms.openlocfilehash: b24828ee1a09ba8e5f657954e11936f124270173
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="forward-device-to-cloud-messages-to-iot-hub-using-azure-iot-edge-on-a-raspberry-pi"></a>Encaminhar mensagens de dispositivo para nuvem para o Hub IoT usando o Azure IoT Edge em um Raspberry Pi

Este passo a passo do [Exemplo de Bluetooth de baixa energia][lnk-ble-samplecode] demonstra como usar o [Azure IoT Edge][lnk-sdk] para:

* Encaminhar telemetria do dispositivo para a nuvem para o Hub IoT de um dispositivo físico.
* Rotear comandos do Hub IoT para um dispositivo físico.

Este passo a passo aborda:

* **Arquitetura**: informações importantes de arquitetura sobre o exemplo de Bluetooth de baixa energia.
* **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

O passo a passo mostra como criar e executar um gateway do IoT Edge em um Raspberry Pi 3 que execute o Raspbian Linux. O gateway é criado usando o IoT Edge. O exemplo usa um dispositivo BLE (Bluetooth de baixa energia) Texas Instruments SensorTag para coletar dados de temperatura.

Ao executar o gateway do IoT Edge, ele:

* Conecta-se a um dispositivo SensorTag usando o protocolo de BLE (Bluetooth de baixa energia).
* Conecta-se ao Hub IoT usando o protocolo HTTPS.
* Encaminha a telemetria do dispositivo SensorTag ao Hub IoT.
* Roteia comandos do Hub IoT para o dispositivo SensorTag.

O gateway contém os seguintes módulos do IoT Edge:

* Um *módulo BLE* que interage com um dispositivo BLE para receber dados de temperatura do dispositivo e envia comandos para o dispositivo.
* Um *módulo BLE nuvem para dispositivo* que converte as mensagens JSON enviadas do Hub IoT em instruções BLE para o *módulo BLE*.
* Um *módulo de agente* que registra todas as mensagens do gateway em um arquivo local.
* Um *módulo de mapeamento de identidade* que faz a conversão entre endereços MAC dos dispositivos BLE e identidades de dispositivos do Hub IoT do Azure.
* Um *módulo do Hub IoT* que carrega dados de telemetria para um Hub IoT e recebe comandos de dispositivo de um Hub IoT.
* Um *módulo de impressora BLE* que interpreta a telemetria do dispositivo BLE e imprime dados formatados para o console para habilitar a solução de problemas e depuração.

### <a name="how-data-flows-through-the-gateway"></a>Como os dados fluem pelo gateway

O diagrama de bloco a seguir ilustra o pipeline do fluxo de dados de upload de telemetria:

![Pipeline de gateway de upload de telemetria](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

As etapas que um item de telemetria realiza ao viajar de um dispositivo BLE para o Hub IoT são:

1. O dispositivo BLE gera uma amostra de temperatura e a envia por Bluetooth para o módulo BLE no gateway.
1. O módulo BLE recebe a amostra e a publica para o agente com o endereço MAC do dispositivo.
1. O módulo de mapeamento de identidade capta essa mensagem e usa uma tabela interna para converter o endereço MAC do dispositivo em uma identidade de dispositivo do Hub IoT. Uma identidade de dispositivo do Hub IoT consiste em uma ID de dispositivo e na chave do dispositivo.
1. O módulo de mapeamento de identidade publica uma nova mensagem que contém as seguintes informações:
   - Dados de exemplo de temperatura
   - O endereço MAC do dispositivo
   - A ID do dispositivo
   - A chave do dispositivo  
1. O módulo de Hub IoT recebe essa nova mensagem (gerada pelo módulo de mapeamento de identidade) e a publica no Hub IoT.
1. O módulo de agente registra todas as mensagens do agente em um arquivo local.

O diagrama de bloco a seguir ilustra o pipeline do fluxo de dados de comando do dispositivo:

![Pipeline do gateway de comando do dispositivo](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. O módulo de Hub IoT periodicamente sonda o Hub IoT quanto a novas mensagens de comando.
1. Quando o módulo de Hub IoT recebe uma nova mensagem de comando, ele a publica no agente.
1. O módulo de mapeamento de identidade capta a mensagem de comando e usa uma tabela interna para converter a ID do dispositivo Hub IoT para um endereço MAC do dispositivo. Em seguida, ele publica uma nova mensagem que inclui o endereço MAC do dispositivo de destino no mapa de propriedades da mensagem.
1. O módulo Nuvem para dispositivo BLE pega essa mensagem e a converte na instrução BLE adequada para o módulo BLE. Em seguida, ele publica uma nova mensagem.
1. O módulo BLE capta essa mensagem e executa a instrução de E/S se comunicando com o dispositivo BLE.
1. O módulo de agente registra todas as mensagens do agente em um arquivo de disco.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [!NOTE]
> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

É necessário um cliente SSH em seu computador desktop para que você possa acessar remotamente a linha de comando no Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos o uso de [PuTTY](http://www.putty.org/).
- A maioria das distribuições do Linux e Mac OS incluem o utilitário de linha de comando do SSH. Para obter mais informações, consulte [SSH usando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

## <a name="prepare-your-hardware"></a>Prepare seu hardware

Este tutorial presume que você esteja usando um dispositivo [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a um Raspberry Pi 3 que executa o Raspbian.

### <a name="install-raspbian"></a>Instalar Raspbian

Você pode usar uma das opções a seguir para instalar o Raspbian em seu dispositivo Raspberry Pi 3.

* Para instalar a versão mais recente do Raspbian, use a interface do usuário gráfica [NOOBS][lnk-noobs].
* [Baixe][lnk-raspbian] e grave manualmente a imagem mais recente do sistema operacional Raspbian em um cartão SD.

### <a name="sign-in-and-access-the-terminal"></a>Entre e acesse o terminal

Você tem duas opções para acessar um ambiente de terminal no seu Raspberry Pi:

* Se você tiver um teclado e um monitor conectado ao seu Raspberry Pi, você pode usar a GUI do Raspbian para acessar uma janela de terminal.

* Acesse a linha de comando em seu Raspberry Pi usando o SSH em seu computador desktop.

#### <a name="use-a-terminal-window-in-the-gui"></a>Use uma janela de terminal na GUI

As credenciais padrões para Raspbian são o nome de usuário **pi** e a senha **raspberry**. Na barra de tarefas na GUI, você pode iniciar o utilitário **Terminal** usando o ícone que se parece com um monitor.

#### <a name="sign-in-with-ssh"></a>Entre com o SSH

Você pode usar o SSH para acesso de linha de comando para o Raspberry Pi. O artigo [SSH (Secure Shell)][lnk-pi-ssh] descreve como configurar SSH em seu Raspberry Pi e como conectar-se a partir do [Windows][lnk-ssh-windows] ou [Linux e Mac OS][lnk-ssh-linux].

Entre com o nome de usuário **pi** e a senha **raspberry**.

### <a name="install-bluez-537"></a>Instalar o BlueZ 5.37

Os módulos BLE se comunicar com o hardware de Bluetooth usando a pilha de BlueZ. Você precisa ter a versão 5.37 do BlueZ para que os módulos funcionem corretamente. Essas instruções fazem com que a versão correta do BlueZ esteja instalada.

1. Pare o daemon do bluetooth atual:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Instale as dependências de BlueZ:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf libtool glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Baixe o código-fonte do BlueZ de bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Descompacte o código-fonte:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Altere os diretórios para a pasta recém-criada:

    ```sh
    cd bluez-5.37
    ```

1. Configure o código BlueZ a ser compilado:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Compile o BlueZ:

    ```sh
    make
    ```

1. Após compilar o BlueZ, instale-o:

    ```sh
    sudo make install
    ```

1. Altere a configuração de serviço systemd para o bluetooth a fim de que ele aponte para o novo daemon de bluetooth no arquivo `/lib/systemd/system/bluetooth.service`. Substitua a linha 'ExecStart' pelo seguinte texto:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Habilitar conectividade para o dispositivo SensorTag de seu dispositivo Raspberry Pi 3

Antes de executar o exemplo, você precisa verificar se seu Raspberry Pi 3 pode se conectar ao dispositivo SensorTag.

1. Verifique se o utilitário `rfkill` está instalado:

    ```sh
    sudo apt-get install rfkill
    ```

1. Desbloqueie o Bluetooth no Raspberry Pi 3 e verifique se o número de versão é **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Para acessar o shell de bluetooth interativo, inicie o serviço de bluetooth e execute o comando **bluetoothctl**:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Digite o comando **power on** para ligar o controlador bluetooth. O comando retorna saídas semelhantes ao seguinte exemplo:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. No shell interativo bluetooth, digite o comando **scan on** para verificar se há dispositivos bluetooth. O comando retorna saídas semelhantes ao seguinte exemplo:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Torne o dispositivo SensorTag detectável pressionando o botão pequeno (o LED verde deve piscar). O Raspberry Pi 3 deve detectar o dispositivo SensorTag:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    Neste exemplo, você pode ver que é o endereço MAC do dispositivo SensorTag é **A0:E6:F8:B5:F6:00**.

1. Desligue a verificação inserindo o comando **scan off**:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Conecte-se ao dispositivo SensorTag usando seu endereço MAC inserindo **connect \<endereço MAC\>**. A saída de exemplo a seguir é abreviada para fins de esclarecimento:

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Você pode listar as características do GATT do dispositivo novamente usando o comando **list-attributes**.

1. Agora você pode se desconectar do dispositivo usando o comando **disconnect** e sair do shell Bluetooth usando o comando **quit**:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora você está pronto para executar o exemplo de BLE IoT Edge no Raspberry Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Executar o exemplo de IoT Edge BLE

Para executar o exemplo de IoT Edge BLE, você precisará concluir três tarefas:

* Configurar dois dispositivos de exemplo em seu Hub IoT.
* Crie o IoT Edge em seu dispositivo Raspberry Pi 3.
* Configure e execute o exemplo de BLE no dispositivo Raspberry Pi 3.

No momento da redação desse artigo, o IoT Edge dava suporte apenas a módulos BLE em gateways em execução no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dois dispositivos de exemplo em seu Hub IoT

* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione um dispositivo chamado **SensorTag_01** ao Hub IoT e anote sua ID e chave de dispositivo. Você pode usar as ferramentas do [iothub-explorer ou o Gerenciador de Dispositivos][lnk-explorer-tools] para adicionar esse dispositivo ao Hub IoT que criou na etapa anterior e para recuperar sua chave. Você mapeia este dispositivo para o dispositivo SensorTag quando configura o gateway.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Criar o Azure IoT Edge no Raspberry Pi 3

Instalar dependências para o Azure IoT Edge:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev libtool
```

Use os seguintes comandos para clonar o IoT Edge e todos os seus submódulos em seu diretório inicial:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Quando você tiver uma cópia completa do repositório do IoT Edge em seu Raspberry Pi 3, poderá criá-lo usando o comando a seguir na pasta que contém o SDK:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurar e execute o exemplo de BLE em seu Raspberry Pi 3

Para inicializar e executar o exemplo, configure cada módulo IoT Edge que participa do gateway. Essa configuração é fornecida em um arquivo JSON e você precisa configurar todos os cinco módulos IoT Edge participantes. Há um arquivo JSON de exemplo no repositório chamado **gateway\_sample.json**, que pode ser usado como ponto de partida para criar seu próprio arquivo de configuração. Esse arquivo está na pasta **samples/ble_gateway/src** na cópia local do repositório do IoT Edge.

As seções a seguir descrevem como editar esse arquivo de configuração para o exemplo de BLE. Presumiremos que o repositório do IoT Edge está na pasta **/home/pi/iot-edge/** no seu Raspberry Pi 3. Se o repositório estiver em outro lugar, ajuste os caminhos adequadamente.

#### <a name="logger-configuration"></a>Configuração do agente

Pressupondo que o repositório de gateway esteja localizado na pasta **/home/pi/iot-edge/**, configure o módulo de agente da seguinte maneira:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configuração do módulo BLE

A configuração de exemplo do dispositivo BLE pressupõe um dispositivo Texas Instruments SensorTag. Qualquer dispositivo BLE padrão que pode operar como um GATT periférico deve funcionar, mas você talvez precise atualizar as IDs de característica GATT e os dados. Adicione o endereço MAC do dispositivo SensorTag:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Se você não estiver usando um dispositivo SensorTag, leia a documentação do seu dispositivo BLE para determinar se é necessário atualizar os valores de dados e IDs características GATT.

#### <a name="iot-hub-module"></a>módulo do Hub IoT

Adicione o nome do Hub IoT. O valor do sufixo é geralmente **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração do módulo de mapeamento de identidade

Adicione o endereço MAC do dispositivo SensorTag e a ID e a chave do dispositivo **SensorTag_01** adicionado ao seu Hub IoT:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração do módulo de impressora BLE

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configuração do módulo BLEC2D

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de roteamento

A configuração abaixo garante o seguinte roteamento entre módulos IoT Edge:

* O módulo **Agente** recebe e registra todas as mensagens.
* O módulo **SensorTag** envia mensagens para os módulos **mapeamento** e **Impressora BLE**.
* O módulo **mapeamento** envia mensagens ao módulo **IoTHub** a ser enviado ao seu Hub IoT.
* O módulo **IoTHub** envia as mensagens de volta para o módulo **mapeamento**.
* O módulo **mapeamento** envia mensagens para o módulo **BLEC2D**.
* O módulo **BLEC2D** envia as mensagens de volta para o módulo **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Para executar o exemplo, passe o caminho até o arquivo de configuração JSON como um parâmetro para o binário **ble\_gateway**. O comando a seguir pressupõe que você esteja usando o arquivo de configuração **gateway_sample.json**. Execute este comando da pasta **iot-edge** no Raspberry Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Talvez seja necessário pressionar o botão pequeno no dispositivo SensorTag para torná-lo detectável antes de executar o exemplo.

Ao executar o exemplo, você pode usar o [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou a ferramenta [iothub-explorer](https://github.com/Azure/iothub-explorer) para monitorar as mensagens que o gateway do IoT Edge encaminha para o dispositivo SensorTag. Por exemplo, usando o iothub-explorer, você pode monitorar as mensagens de dispositivo para nuvem usando o seguinte comando:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

O módulo BLE também dá suporte ao envio de comandos do Hub IoT para o dispositivo. Você pode usar a ferramenta [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou o [iothub-explorer](https://github.com/Azure/iothub-explorer) para enviar as mensagens JSON que o módulo de gateway BLE encaminha para o dispositivo BLE.

Se você estiver usando o dispositivo Texas Instruments SensorTag, você poderá ativar o LED vermelho, o LED verde ou a campainha enviando comandos do Hub IoT. Antes de enviar comandos do Hub IoT, primeiro envie as duas mensagens JSON a seguir nesta ordem. Em seguida, você pode enviar um dos comandos para ligar as luzes ou a campainha.

1. Redefinir todos os LEDs e a campainha (desativá-los):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Configurar a E/S como 'remota':

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Agora, você pode enviar um dos seguintes comandos para ligar as luzes ou a campainha no dispositivo SensorTag:

* Ativar o LED vermelho:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Ativar o LED verde:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Ativar a campainha:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Se você quiser compreender de maneira mais avançada o IoT Edge e experimentar alguns exemplos de código, acesse os seguintes recursos e tutoriais para desenvolvedores:

* [Azure IoT Edge][lnk-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
