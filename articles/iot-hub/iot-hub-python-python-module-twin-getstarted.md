---
title: Introdução ao módulo gêmeo e à identidade do módulo do Hub IoT (Python) | Microsoft Docs
description: Saiba como criar identidade do módulo e atualizar o módulo gêmeo usando SDKs do IoT para Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: f887fbd4f82e59c02d6a5b69d0d5b43b426a39bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441196"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Introdução ao módulo gêmeo e à identidade do módulo do Hub IoT usando back-end Python e dispositivo Python

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo gêmeo do Hub IoT do Azure permitem que o aplicativo de back-end configure um dispositivo e forneça visibilidade às condições do dispositivo, uma identidade de módulo e módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos habilitados com vários componentes, como dispositivos baseados em sistema operacional ou dispositivos de firmware, permite condições e configuração isolada para cada componente.
>

No fim deste tutorial, você tem dois aplicativos de Python:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de dispositivo e módulo.

* **UpdateModuleTwinReportedProperties**, que envia propriedades relatadas atualizadas de módulo gêmeo ao Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).
>

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Um Hub IoT.

* Instale o [SDK do Python](https://github.com/Azure/azure-iot-sdk-python) mais recente.

Agora, você criou seu hub IoT e tem o nome de host e a cadeia de conexão de Hub IoT que precisa para concluir o restante deste tutorial.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade do dispositivo e uma identidade do módulo no Hub IoT

Nesta seção, você criará um aplicativo Python que cria uma identidade do dispositivo e uma identidade do módulo no registro de identidade no Hub IoT. Um dispositivo ou módulo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "Registro de identidade" de [guia do desenvolvedor do IoT Hub](iot-hub-devguide-identity-registry.md). Quando você executa esse aplicativo de console, ele gera ID e chave exclusivas para o dispositivo e o módulo. O dispositivo e o módulo usam esses valores para se identificar ao enviar mensagens de dispositivo para nuvem para o Hub IoT. As IDs diferenciam minúsculas e maiúsculas.

Adicione o código a seguir ao arquivo do Python:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Esse aplicativo cria uma identidade do dispositivo com a ID **myFirstDevice** e uma identidade do módulo com a ID **myFirstModule** no dispositivo **myFirstDevice**. (se essa ID de módulo já existir no registro de identidade, o código simplesmente irá recuperar as informações do módulo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de módulo simulado para se conectar ao Hub IoT.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo e módulo para habilitar o acesso seguro ao Hub IoT. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não há nenhum sinalizador habilitado/desabilitado para as identidades do módulo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo Python

Nesta seção, você criará um aplicativo Python no dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

1. **Obter a cadeia de conexão do módulo** – agora, se você entrar a [portal do Azure](https://portal.azure.com/). Navegue até seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice e abra-o. Você verá que myFirstModule foi criado com êxito. Copie a cadeia de conexão do módulo. Ela será necessária na próxima etapa.

   ![Detalhes do módulo do Portal do Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Criar aplicativo UpdateModuleTwinReportedProperties**

   Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP.

## <a name="get-updates-on-the-device-side"></a>Obter atualizações no lado do dispositivo

Além do código acima, é possível adicionar o bloco de código abaixo para obter a mensagem de atualização gêmea no dispositivo.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)