---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Python) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização do dispositivo remoto. Use o SDK do IoT do Azure para Python para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: c07d26715aff2c8dd5e00a7d7adbb548adf00a28
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482493"
---
# <a name="get-started-with-device-management-python"></a>Introdução ao gerenciamento de dispositivos (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.

* Crie um aplicativo de dispositivo simulado contendo um método direto que reinicia o dispositivo. Métodos diretos são invocados da nuvem.

* Criar um aplicativo de console Python que chama um método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **dmpatterns_getstarted_device.py**, que conecta seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **dmpatterns_getstarted_service.py**, que chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você:

* Criar um aplicativo de console do Python que responde a um método direto chamado pela nuvem

* Simular a reinicialização de um dispositivo

* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e a última reinicialização

1. No seu prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-device**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando um editor de texto, crie um arquivo chamado **dmpatterns_getstarted_service.py** em seu diretório de trabalho.

3. Adicione as seguintes instruções `import` no início do arquivo **dmpatterns_getstarted_device.py**.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adicione a variável **CONNECTION_STRING**. Substitua o valor de espaço reservado `{deviceConnectionString}` pela cadeia de conexão do dispositivo. Você copiou essa cadeia de conexão anteriormente em [Registrar um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adicione os retornos de chamada da função para implementar o método direto no dispositivo.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Inicie o ouvinte do método direto e aguarde.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Salve e feche o arquivo **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você criará um aplicativo do console Python que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo gêmeo para descobrir o último horário de reinicialização para esse dispositivo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-hub**:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_service.py** em seu diretório de trabalho.

3. Adicione as seguintes instruções `import` no início do arquivo **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adicione as declarações de variável a seguir. Substitua o valor de espaço reservado `{IoTHubConnectionString}` pela cadeia de conexão do hub IoT que você copiou anteriormente em [Obter a cadeia de conexão do hub IoT](#get-the-iot-hub-connection-string). Substitua o valor do espaço reservado `{deviceId}` pela ID do dispositivo que você registrou em [Registrar um novo dispositivo no Hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adicione a função a seguir para invocar o método de dispositivo para reiniciar o dispositivo de destino e consultar os dispositivos gêmeos, além de obter a hora de reinicialização mais recente.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Salve e feche o arquivo **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando, execute o seguinte comando para começar a escutar o método direto de reinicialização.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Em outro prompt de comando, execute o seguinte comando para disparar a reinicialização e a consulta remotas para que o dispositivo gêmeo localize o tempo de reinicialização mais recente.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Você verá a resposta do dispositivo para o método direto no console.

   O seguinte item mostra a resposta do dispositivo para o método direto de reinicialização:

   ![Saída do aplicativo de dispositivo simulado](./media/iot-hub-python-python-device-management-get-started/device.png)

   O seguinte item mostra o serviço chamando o método direto de reinicialização e sondando o status no dispositivo gêmeo:

   ![Disparar saída do serviço de reinicialização](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
