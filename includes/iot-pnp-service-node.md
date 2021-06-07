---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487770"
---
O IoT Plug and Play simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Node.js para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido, você precisa do Node.js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas de [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonar o repositório do SDK com o código de exemplo

Clone os exemplos de um [repositório do SDK do Node](https://github.com/Azure/azure-iot-sdk-node). Abra uma janela de terminal em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do IoT do Microsoft Azure para Node.js](https://github.com/Azure/azure-iot-sdk-node):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Neste início rápido, você usa um dispositivo de termostato de exemplo que é escrito em Node.js como o dispositivo do IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela de terminal e navegue até a pasta local que contém o SDK de IoT do Microsoft Azure para o repositório do Node.js clonado do GitHub.

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-node/device/samples/pnp*. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Execute o dispositivo de termostato de exemplo com o seguinte comando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele para confirmar se a amostra de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Em [Configurar o ambiente para os inícios rápidos e os tutoriais do IoT Plug and Play](../articles/iot-pnp/set-up-environment.md), você criou duas variáveis de ambiente para configurar o exemplo a ser conectado ao hub IoT e ao dispositivo:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Neste início rápido, você usará uma solução de IoT de exemplo em Node.js para interagir com o dispositivo de exemplo que acabou de configurar.

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**.

1. No repositório clonado do SDK do Node, navegue até a pasta */azure-iot-sdk-node/service/samples/javascript*. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você executou o dispositivo termostato de exemplo no terminal do **dispositivo**, você viu as seguintes mensagens indicando seu status online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Vá para o terminal de **serviço** e use o seguinte comando para executar a amostra de leitura de informações do dispositivo:

    ```cmd/sh
    node twin.js
    ```

1. No saída de terminal do **serviço**, observe a resposta do dispositivo gêmeo. Você verá a ID do modelo do dispositivo e as propriedades associadas relatadas:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. O seguinte snippet mostra o código em *twin.js* que recupera a ID do modelo do dispositivo gêmeo:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

Neste cenário, ele gera `Model Id: dtmi:com:example:Thermostat;1`.

> [!NOTE]
> Esses exemplos de serviço usam a classe **Registry** do **cliente do serviço do Hub IoT**. Para saber mais sobre as APIs, incluindo a API de gêmeos digitais, confira o [guia do desenvolvedor do serviço](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Abra o arquivo *twin.js* em um editor de código.

1. Examine o código de exemplo; ele mostra duas formas para atualizar o dispositivo gêmeo. Para usar a primeira, modifique a variável `twinPatch` da seguinte maneira:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    A propriedade `targetTemperature` é definida como uma propriedade gravável no modelo de dispositivo Termostato.

1. No terminal de **serviço**, use o seguinte comando para executar a amostra de atualização da propriedade:

    ```cmd/sh
    node twin.js
    ```

1. No terminal do **dispositivo**, você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. No terminal de **serviço**, execute o seguinte comando para confirmar se a propriedade está atualizada:

    ```cmd/sh
    node twin.js
    ```

1. No saída de terminal de **serviço**, na seção de propriedades`reported`, você verá a temperatura de destino atualizada relatada. Pode demorar algum tempo para que o dispositivo conclua a atualização. Repita essa etapa até que o dispositivo tenha processado a atualização da propriedade:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o arquivo *device_method.js* e examine o código.

1. Vá para o terminal de **serviço**. Use o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. A saída no terminal de **serviço** mostra a seguinte confirmação:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. No terminal do **dispositivo**, você verá que o comando foi confirmado:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
