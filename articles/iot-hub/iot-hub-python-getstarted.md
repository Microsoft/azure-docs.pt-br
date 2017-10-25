---
title: "Introdução ao Hub IoT do Azure (Python) | Microsoft Docs"
description: "Saiba como enviar mensagens de dispositivo para nuvem para o Hub IoT do Azure usando os SDKs da IoT para Python. Crie um dispositivo simulado e aplicativos de serviço para registrar seu dispositivo, envie mensagens e leia mensagens no hub IoT."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: 7ebbac4464d793717f68a4cb7905c53d1f5c051a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Conecte seu dispositivo simulado ao Hub IoT usando Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No fim deste tutorial, você terá dois aplicativos de Python:

* **CreateDeviceIdentity.py**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o aplicativo de dispositivo simulado.
* **SimulatedDevice.py**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e, periodicamente, envia uma mensagem de telemetria usando o protocolo MQTT.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* [Node.js 4.0 ou posterior][lnk-node-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Isso é necessário para instalar a [ferramenta do Gerenciador de Hub IoT][lnk-iot-hub-explorer].
* Uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` atualmente estão disponíveis apenas para o sistema operacional Windows. Para Linux/Mac OS, confira as seções do Linux e Mac OS específicas na publicação [Preparação do seu ambiente de desenvolvimento para o Python][lnk-python-devbox].
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Você criou seu Hub IoT. Use o nome de host do Hub IoT e a cadeia de conexão do Hub IoT para o restante deste tutorial.

> [!NOTE]
> Você também pode criar facilmente seu hub IoT em uma linha de comando, usando a CLI do Azure baseada em Python ou Node. js. O artigo [Criar um hub IoT usando o Azure CLI 2.0][lnk-azure-cli-hub] mostra os passos rápidos para fazê-lo. 
> 

## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo
Esta seção lista as etapas para criar um aplicativo do console do Python que cria uma identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo só pode se conectar ao Hub IoT se tiver uma entrada no registro de identidade. Para obter mais informações, consulte a seção **"Registro de identidade"** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Quando você executar esse aplicativo de console, ele irá gerar uma ID e chave do dispositivo exclusivas com as quais seu dispositivo poderá identificar-se ao enviar mensagens entre o dispositivo e a nuvem para o Hub IoT.

1. Abra um prompt de comando e instale o **SDK de Serviço do Azure IoT Hub para Python**. Feche o prompt de comando depois de instalar o SDK.

    ```
    pip install azure-iothub-service-client
    ```

2. Crie um arquivo Python chamado **CreateDeviceIdentity.py**. Abra-o em [um editor/IDE do Python de sua escolha][lnk-python-ide-list], por exemplo, o padrão [OCIOSO][lnk-idle].

3. Adicione o código a seguir para importar os módulos necessários do SDK de serviço:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Adicione o código abaixo, substituindo o espaço reservado para `[IoTHub Connection String]` pela cadeia de conexão para o Hub IoT criada na seção anterior. Você pode usar qualquer nome como o `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Adicione a seguinte função para imprimir algumas das informações do dispositivo.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Adicione a seguinte função para criar a identificação de dispositivo usando o Gerenciador de Registro. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Finalmente, adicione a função principal da seguinte maneira e salve o arquivo.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. No prompt de comando, execute **CreateDeviceIdentity.py** da seguinte maneira:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Você deve ver o dispositivo simulado sendo criado. Anote o **deviceId** e a **primaryKey** desse dispositivo. Você precisará desses valores mais tarde quando criar um aplicativo que conecta o Hub IoT como um dispositivo.

    ![Criar acesso ao dispositivo][1]

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira o [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Esta seção lista as etapas para criar um aplicativo do console do Python que simula um dispositivo e envia mensagens do dispositivo para a nuvem para o seu hub IoT.

1. Abra um novo prompt de comando e instale o SDK do Dipositivo do Azure IoT Hub para Python conforme a seguir. Feche o prompt de comando após a instalação.

    ```
    pip install azure-iothub-device-client
    ```
2. Crie um arquivo chamado **SimulatedDevice.py**. Abra-o em um editor/IDE do Python de sua escolha (por exemplo, OCIOSO).

3. Adicione o código a seguir para importar os módulos necessários do SDK do dispositivo.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Adicione o código a seguir e substitua o espaço reservado por `[IoTHub Device Connection String]` pela cadeia de conexão para o seu dispositivo. A cadeia de conexão do dispositivo normalmente está no formato `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Use o **deviceId** e a **primaryKey** do dispositivo que você criou na seção anterior para substituir `<deviceId>` e `<primaryKey>` respectivamente. Substitua `<hostName>` pelo nome do host do seu hub IoT, normalmente `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Adicione o seguinte código para definir um retorno de chamada de confirmação de envio. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Adicione o seguinte código para inicializar o cliente do dispositivo.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. Adicione a seguinte função para formatar e enviar uma mensagem do dispositivo simulado em seu hub IoT.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Finalmente, adicione a função principal. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Salve e feche o arquivo **SimulatedDevice.py**. Agora você está pronto para executar o aplicativo.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Receber mensagens do dispositivo simulado
Para receber mensagens de telemetria do seu dispositivo, você precisa usar um ponto de extremidade compatível com [Hubs de eventos][lnk-event-hubs-overview] exposto pelo Hub IoT, que lê as mensagens do dispositivo para a nuvem. Leia o tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] para saber informações sobre como processar as mensagens dos Hubs de Eventos para o ponto de extremidade compatível com o Hub de Eventos do Hub IoT. Os Hubs de Eventos não suportam telemetria em Python, então você pode criar um [Node. js](iot-hub-node-node-getstarted.md#D2C_node) ou um aplicativo de console baseado em Hubs de Eventos do [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) para ler as mensagens do dispositivo para a nuvem de Hub IoT. Este tutorial mostra como você pode usar a [ferramenta Gerenciador de Hub IoT][lnk-iot-hub-explorer] para ler essas mensagens de dispositivo.

1. Abra um prompt de comando e instale o Gerenciador de Hub IoT. 

    ```
    npm install -g iothub-explorer
    ```

2. Execute o seguinte comando no prompt de comando, para começar a monitorar as mensagens do dispositivo para a nuvem a partir do seu dispositivo. Use a cadeia de conexão do seu hub IoT no espaço reservado após `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Abra um novo prompt de comando e navegue até o diretório que contém o arquivo **SimulatedDevice.py**.

4. Execute o arquivo **SimulatedDevice.py**, que envia periodicamente os dados de telemetria para o seu hub IoT. 
   
    ```
    python SimulatedDevice.py
    ```
5. Observe as mensagens de dispositivo no prompt de comando executando o Gerenciador de Hub IoT da seção anterior. 

    ![Mensagens do dispositivo para a nuvem de Python][2]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo de dispositivo simulado para enviar as mensagens entre o dispositivo e a nuvem para o Hub IoT. Você observou as mensagens recebidas pelo hub IoT com a Ajuda da ferramenta do Gerenciador de Hub IoT. 

Para explorar o SDK do Python para uso do Hub IoT do Azure em detalhes, visite [este repositório do Git Hub][lnk-python-github]. Para examinar os recursos de mensagens do SDK de serviço do Hub IoT do Azure para Python, você pode baixar e executar [iothub_messaging_sample.py][lnk-messaging-sample]. Para a simulação do lado do dispositivo usando o SDK do dispositivo de Hub IoT do Azure para Python, você pode baixar e executar [iothub_client_sample.py][lnk-client-sample].

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Conectando o dispositivo][lnk-connect-device]
* [Introdução ao gerenciamento de dispositivo][lnk-device-management]
* [Introdução ao Azure IoT Edge][lnk-iot-edge]

Para saber como estender sua solução IoT e processar as mensagens entre o dispositivo e a nuvem em escala, consulte o tutorial [Processar as mensagens entre o dispositivo e a nuvem][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
