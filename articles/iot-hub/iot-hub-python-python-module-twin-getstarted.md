---
title: Módulo e a identidade do módulo do Hub IoT do Azure (Python)
description: Saiba como criar identidade do módulo e atualizar o módulo gêmeo usando SDKs do IoT para Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92139400"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Introdução à identidade e módulo do módulo do Hub IoT (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os gêmeos de módulo](iot-hub-devguide-module-twins.md) são semelhantes às identidades de dispositivo do Hub IOT do Azure e dispositivos gêmeos, mas fornecem granularidade mais fina. Enquanto as identidades de dispositivo do Hub IoT do Azure e dispositivos gêmeos permitem que um aplicativo de back-end configure um dispositivo e forneça visibilidade sobre as condições do dispositivo, as identidades do módulo e o módulo gêmeos fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, como dispositivos baseados no sistema operacional ou dispositivos de firmware, eles permitem a configuração isolada e as condições para cada componente.
>

No final deste tutorial, você tem três aplicativos Python:

* **Createmodule**, que cria uma identidade de dispositivo, uma identidade de módulo e chaves de segurança associadas para conectar seus clientes de módulo e de dispositivo.

* **UpdateModuleTwinDesiredProperties**, que envia as propriedades desejadas do módulo atualizado para o Hub IOT.

* **ReceiveModuleTwinDesiredPropertiesPatch**, que recebe o patch das propriedades desejadas do módulo "/" em seu dispositivo.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

Neste artigo, você cria um serviço de back-end que adiciona um dispositivo no registro de identidade e, em seguida, adiciona um módulo a esse dispositivo. Esse serviço requer a permissão de **gravação do registro** (que também inclui a **leitura do registro**). Você também cria um serviço que adiciona as propriedades desejadas ao módulo e... para o módulo recém-criado. Esse serviço precisa da permissão de **conexão de serviço** . Embora existam políticas de acesso compartilhado padrão que concedem essas permissões individualmente, nesta seção, você cria uma política de acesso compartilhado personalizada que contém essas duas permissões.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade do dispositivo e uma identidade do módulo no Hub IoT

Nesta seção, você cria um aplicativo de serviço Python que cria uma identidade de dispositivo e uma identidade de módulo no registro de identidade em seu hub IoT. Um dispositivo ou módulo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade. Para obter mais informações, consulte [entender o registro de identidade em seu hub IOT](iot-hub-devguide-identity-registry.md). Quando você executa esse aplicativo de console, ele gera ID e chave exclusivas para o dispositivo e o módulo. O dispositivo e o módulo usam esses valores para se identificar ao enviar mensagens de dispositivo para nuvem para o Hub IoT. As IDs diferenciam minúsculas e maiúsculas.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-hub**:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. No prompt de comando, execute o seguinte comando para instalar o pacote **msrest** . Você precisa desse pacote para capturar exceções **HTTPOperationError** .

    ```cmd/sh
    pip install msrest
    ```

1. Usando um editor de texto, crie um arquivo chamado **CreateModule.py** em seu diretório de trabalho.

1. Adicione o código a seguir ao seu arquivo Python. Substitua *YourIoTHubConnectionString* pela cadeia de conexão que você copiou em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. No prompt de comando, execute o seguinte comando:

    ```cmd/sh
    python CreateModule.py
    ```

Esse aplicativo cria uma identidade do dispositivo com a ID **myFirstDevice** e uma identidade do módulo com a ID **myFirstModule** no dispositivo **myFirstDevice**. (Se o dispositivo ou a ID do módulo já existir no registro de identidade, o código simplesmente recuperará as informações existentes do dispositivo ou do módulo.) O aplicativo exibe a ID e a chave primária para cada identidade.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo e módulo para habilitar o acesso seguro ao Hub IoT. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não há nenhum sinalizador habilitado/desabilitado para as identidades do módulo. Para obter mais informações, consulte [entender o registro de identidade em seu hub IOT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Atualizar o módulo "/" usando o SDK do serviço Python

Nesta seção, você cria um aplicativo de serviço do Python que atualiza as propriedades desejadas do módulo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **Azure-IOT-Hub** . Você poderá ignorar esta etapa se tiver instalado o pacote **Azure-IOT-Hub** na seção anterior.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Usando um editor de texto, crie um arquivo chamado **UpdateModuleTwinDesiredProperties.py** em seu diretório de trabalho.

1. Adicione o código a seguir ao seu arquivo Python. Substitua *YourIoTHubConnectionString* pela cadeia de conexão que você copiou em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Obter atualizações no lado do dispositivo

Nesta seção, você criará um aplicativo Python para obter a atualização das propriedades desejadas do módulo "r" em seu dispositivo.

1. Obtenha a cadeia de conexão do módulo. Em [portal do Azure](https://portal.azure.com/), navegue até o Hub IOT e selecione **dispositivos IOT** no painel esquerdo. Selecione **myFirstDevice** na lista de dispositivos e abra-o. Em **identidades do módulo**, selecione **myFirstModule**. Copie a cadeia de conexão do módulo. Você precisará dela em uma etapa a seguir.

   ![Detalhes do módulo do Portal do Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. No seu prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-device**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Usando um editor de texto, crie um arquivo chamado **ReceiveModuleTwinDesiredPropertiesPatch.py** em seu diretório de trabalho.

1. Adicione o código a seguir ao seu arquivo Python. Substitua *YourModuleConnectionString* pela cadeia de conexão do módulo que você copiou na etapa 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Executar os aplicativos

Nesta seção, você executará o aplicativo de dispositivo **ReceiveModuleTwinDesiredPropertiesPatch** e executará o aplicativo de serviço **UpdateModuleTwinDesiredProperties** para atualizar as propriedades desejadas do seu módulo.

1. Abra um prompt de comando e execute o aplicativo do dispositivo:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Saída inicial do aplicativo do dispositivo](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Abra um prompt de comando separado e execute o aplicativo de serviço:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Observe que a propriedade desired **TelemetryInterval** aparece no módulo atualizado em... na saída do aplicativo de serviço:

   ![Saída do aplicativo de serviço](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    A mesma propriedade aparece no patch das propriedades desejadas recebidas na saída do aplicativo do dispositivo:

   ![A saída do aplicativo do dispositivo mostra o patch das propriedades desejadas](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/quickstart-linux.md)