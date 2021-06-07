---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95511612"
---
Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao hub IoT e usar a ferramenta Azure IoT Explorer para exibir a telemetria que ele envia. O aplicativo de exemplo é gravado em Python e é incluído no SDK do Dispositivo do Hub IoT do Azure para Python. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

No ambiente Python local, instale o pacote da seguinte maneira:

```cmd/sh
pip install azure-iot-device
```

Clone o repositório de IoT do SDK do Python e faça check-out de **master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* contém o código de exemplo do dispositivo IoT Plug and Play. Este início rápido usa o arquivo *simple_thermostat.py*. Este código de exemplo implementa um dispositivo IoT Plug and Play compatível e usa a Biblioteca de Clientes do Dispositivo Python do IoT do Azure.

Abra o arquivo **simple_thermostat.py** em um editor de texto. Observe como ele:

1. Define um DTMI (identificador de modelo de dispositivo gêmeo) que representa exclusivamente o [Termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Um DTMIs precisa ser conhecido pelo usuário e varia dependendo do cenário de implementação do dispositivo. Para o exemplo atual, o modelo representa um termostato que tem telemetria, propriedades e comandos associados à temperatura de monitoramento.

1. Tem funções para definir implementações de manipulador de comando. Grave esses manipuladores para definir como o dispositivo responde às solicitações de comando.

1. Tem uma função para definir uma resposta de comando. Crie funções de resposta de comando para enviar uma resposta de volta para o hub IoT.

1. Define uma função de ouvinte de teclado de entrada para permitir que você encerre o aplicativo.

1. Tem uma função **main**. A função **main**:

    1. Usa o SDK do dispositivo para criar um cliente de dispositivo e conectar-se ao hub IoT.

    1. Atualiza as propriedades. O modelo que estamos usando, **Termostato**, define `targetTemperature` e `maxTempSinceLastReboot` como as duas propriedades do nosso Termostato, então usaremos elas. As propriedades são atualizadas usando o método `patch_twin_reported_properties` definido no `device_client`.

    1. Inicia a escuta de solicitações de comando usando a função **execute_command_listener**. A função configura um 'ouvinte' para ouvir comandos provenientes do serviço. Ao configurar o ouvinte, você fornece `method_name`, `user_command_handler` e `create_user_response_handler`.
        - A função `user_command_handler` define o que o dispositivo deve fazer quando recebe um comando. Por exemplo, se o alarme for desativado, o efeito de receber esse comando será o seu alerta. Considere isso como o efeito do comando que está sendo invocado.
        - A função `create_user_response_handler` cria uma resposta a ser enviada ao hub IoT quando um comando é executado com êxito. Por exemplo, se o alarme for desativado, você responderá pressionando adiar, que é um feedback para o serviço. Considere isso como a resposta que você fornece ao serviço. Você pode vê-la no portal.

    1. Inicia o envio de telemetria. O **pnp_send_telemetry** é definido no arquivo pnp_methods. O código de exemplo usa um loop para chamar essa função a cada oito segundos.

    1. Desabilita todos os ouvintes e tarefas e sai do loop quando você pressiona **Q** ou **q**.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Agora que você já viu o código, use o seguinte comando para executar o exemplo:

```cmd/sh
python simple_thermostat.py
```

Você vê a seguinte saída, que indica que o dispositivo está enviando dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
