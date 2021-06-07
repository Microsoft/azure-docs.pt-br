---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487769"
---
O IoT Plug and Play simplifica a IoT ao permitir que você interaja com o modelo de um dispositivo sem conhecer a implementação subjacente dele. Este início rápido mostra como usar o Python para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

O pacote **azure-iot-device** é publicado como um PIP.

No ambiente Python local, instale o pacote da seguinte maneira:

```cmd/sh
pip install azure-iot-device
```

Instale o pacote **azure-iot-hub** executando o seguinte comando:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Neste início rápido, você usará um dispositivo de termostato gravado em Python como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela de terminal em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Python do IoT do Azure](https://github.com/Azure/azure-iot-sdk-python) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Execute o dispositivo de termostato de exemplo com o seguinte comando:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele para confirmar se a amostra de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Python para interagir com o dispositivo de exemplo que acabou de configurar.

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**.

1. Navegue até a pasta */azure-iot-sdk-python/azure-iot-hub/samples* do repositório clonado do SDK do Python.

1. Abra o arquivo *registry_manager_pnp_sample.py* e examine o código. Este exemplo mostra como usar a classe **IoTHubRegistryManager** para interagir com o dispositivo do IoT Plug and Play.

> [!NOTE]
> Esses exemplos de serviço usam a classe **IoTHubRegistryManager** do **cliente do serviço do Hub IoT**. Para saber mais sobre as APIs, incluindo a API de gêmeos digitais, confira o [guia do desenvolvedor do serviço](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Obter o dispositivo gêmeo

Em [Configurar o ambiente para os inícios rápidos e os tutoriais do IoT Plug and Play](../articles/iot-pnp/set-up-environment.md), você criou duas variáveis de ambiente para configurar o exemplo a ser conectado ao hub IoT e ao dispositivo:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Use o seguinte comando no terminal do **serviço** para executar este exemplo:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Se você estiver executando esse exemplo no Linux, use `export` no lugar de `set`.

A saída mostra o dispositivo gêmeo e imprime a sua ID de modelo:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

O seguinte snippet mostra o código de exemplo de *registry_manager_pnp_sample.py*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Atualizar um dispositivo gêmeo

Este exemplo mostra como atualizar a propriedade `targetTemperature` gravável no dispositivo:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Você pode verificar se a atualização é aplicada no terminal do **dispositivo** que mostra a saída seguir:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

O terminal do **serviço** confirma que o patch foi bem-sucedido:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Invocar um comando

Em seguida, o exemplo invoca um comando:

O terminal do **serviço** mostra uma mensagem de confirmação do dispositivo:

```cmd/sh
The device method has been successfully invoked
```

No terminal do **dispositivo**, você vê que o dispositivo recebe o comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
