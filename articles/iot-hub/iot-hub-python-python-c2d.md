---
title: Mensagens da nuvem para dispositivo com o Hub IoT do Azure (Python) | Microsoft Docs
description: Como enviar mensagens da nuvem para um dispositivo em um hub IoT do Azure usando os SDKs de IoT do Azure para Python. Modifique um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87876794"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Enviar mensagens da nuvem para dispositivo com o Hub IoT (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) mostra como criar um hub IoT, como provisionar uma identidade do dispositivo nele e como codificar um aplicativo de dispositivo simulado que envia mensagens de dispositivo para nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Ele mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

Encontre mais informações sobre mensagens de nuvem para dispositivo no [guia do desenvolvedor do Hub IoT](iot-hub-devguide-messaging.md).

Ao final deste tutorial, você executará dois aplicativos de console do Python:

* **SimulatedDevice.py**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IOT e recebe mensagens da nuvem para o dispositivo.

* **SendCloudToDeviceMessage.py**, que envia mensagens da nuvem para o dispositivo para o aplicativo de dispositivo simulado por meio do Hub IOT.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você cria um aplicativo de console do Python para simular o dispositivo e receber mensagens da nuvem para dispositivo do hub IoT.

1. Em um prompt de comando em seu diretório de trabalho, instale o **SDK do dispositivo do Hub IOT do Azure para Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Usando um editor de texto, crie um arquivo chamado **SimulatedDevice.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SimulatedDevice.py**:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Adicione o código a seguir ao arquivo **SimulatedDevice.py**. Substitua o `{deviceConnectionString}` valor do espaço reservado pela cadeia de conexão do dispositivo para o dispositivo criado no início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md) :

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adicione a seguinte função para imprimir as mensagens recebidas para o console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Adicione o seguinte código para inicializar o cliente e aguarde para receber a mensagem da nuvem para o dispositivo:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Salve e feche o arquivo **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens de nuvem para dispositivo por meio do Hub IoT criado no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Para enviar mensagens de nuvem para dispositivo, o serviço precisa da permissão **conexão de serviço**. Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Nesta seção, você cria um aplicativo do console do Python que envia mensagens da nuvem para dispositivo ao aplicativo de dispositivo simulado. Você precisa da ID do dispositivo que você adicionou no início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md). Você também precisa da cadeia de conexão do hub IoT que você copiou anteriormente em [Obter a cadeia de conexão do hub IoT](#get-the-iot-hub-connection-string).

1. No diretório de trabalho, abra um prompt de comando e instale o **SDK do serviço de Hub IOT do Azure para Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Usando um editor de texto, crie um arquivo chamado **SendCloudToDeviceMessage.py**.

1. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SendCloudToDeviceMessage.py**:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Adicione o código a seguir ao arquivo **SendCloudToDeviceMessage.py**. Substitua os `{iot hub connection string}` `{device id}` valores de espaço reservado e pela cadeia de conexão do Hub IOT e ID do dispositivo observado anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adicione o seguinte código para enviar mensagens ao seu dispositivo:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Salve e feche o arquivo **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando do diretório de trabalho, execute o seguinte comando para escutar mensagens da nuvem para o dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar um aplicativo de dispositivo simulado](./media/iot-hub-python-python-c2d/device-1.png)

1. Abra um novo prompt de comando em seu diretório de trabalho e execute o seguinte comando para enviar mensagens da nuvem para o dispositivo:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Execute o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-python-python-c2d/service.png)

1. Observe as mensagens recebidas pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).
