---
title: Agendar trabalhos com o Hub IoT do Azure (Python) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Você pode usar os SDKs do IoT do Azure para Python para implementar os aplicativos do dispositivo simulado e um aplicativo de serviço para executar o trabalho.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876675"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Agendar e difundir trabalhos (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Hub IoT do Azure é um serviço completamente gerenciado que permite que um aplicativo back-end crie e controle trabalhos que agendam e atualizam milhões de dispositivos.  Os trabalhos podem ser usados para as seguintes ações:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Conceitualmente, um trabalho encapsula uma dessas ações e rastreia o progresso da execução com relação a um conjunto de dispositivos, definido por uma consulta do dispositivo gêmeo.  Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta do dispositivo gêmeo e agendado no futuro.  Esse aplicativo pode acompanhar o progresso à medida que cada um desses dispositivos recebem e executam o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

* Dispositivo gêmeo e propriedades: [Introdução aos dispositivos gêmeos](iot-hub-python-twin-getstarted.md) e [Tutorial: Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)

* Métodos diretos: [Guia do desenvolvedor do Hub IoT – métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: métodos diretos](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Criar um aplicativo do dispositivo simulado Python que tem um método direto para habilitar o **lockDoor** que pode ser chamado pelo back-end da solução.

* Crie um aplicativo de console Python que chama o método direto **lockDoor** no aplicativo de dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

No fim deste tutorial, você tem dois aplicativos de Python:

**simDevice.py**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor**.

**scheduleJobService.py**, que chama um método direto no aplicativo do dispositivo simulado e atualiza as propriedades desejadas do dispositivo gêmeo usando um trabalho.

> [!NOTE]
> O **SDK do IoT do Azure para Python** não dá suporte diretamente à funcionalidade **Trabalhos**. Em vez disso, este tutorial oferece uma solução alternativa usando temporizadores e segmentos assíncronos. Para obter informações mais atualizadas, confira a lista de recursos do **SDK de cliente do serviço** na página [SDK do IoT do Azure para Python](https://github.com/Azure/azure-iot-sdk-python).
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console do Python que responde a um método direto chamado pela nuvem, que aciona um método **lockDoor** simulado.

1. No seu prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-device**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando um editor de texto, crie um novo arquivo **simDevice.py** no diretório de trabalho.

3. Adicione as instruções `import` e variáveis a seguir ao início do arquivo **simDevice.py**. Substitua `deviceConnectionString` pela cadeia de conexão do dispositivo criada acima:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adicione o seguinte retorno de chamada de função para manipular o método **lockDoor**:

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Adicione outra retorno de chamada de função para manipular atualizações de dispositivos gêmeos:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Adicione o código a seguir para registrar o manipulador para o método **lockDoor** . Também inclua a rotina `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Salve e feche o arquivo **simDevice.py**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

Neste artigo, você cria um serviço de back-end que invoca um método direto em um dispositivo e atualiza o dispositivo de atualização. O serviço precisa da permissão de **conexão de serviço** para chamar um método direto em um dispositivo. O serviço também precisa das permissões de **leitura** do registro e de **gravação do registro** para ler e gravar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas permissões, portanto, você precisa criar uma.

Para criar uma política de acesso compartilhado que conceda permissões de conexão do **serviço**, **leitura do registro**e **gravação do registro** e para obter uma cadeia de conexão para essa política, siga estas etapas:

1. Abra o Hub IoT no [portal do Azure](https://portal.azure.com). A maneira mais fácil de acessar o Hub IoT é selecionar grupos de **recursos**, selecionar o grupo de recursos onde o Hub IOT está localizado e, em seguida, selecionar o Hub IOT na lista de recursos.

2. No painel do lado esquerdo do hub IoT, selecione **Políticas de acesso compartilhado**.

3. No menu superior acima da lista de políticas, selecione **Adicionar**.

4. No painel **Adicionar uma política de acesso compartilhado** , insira um nome descritivo para a política; por exemplo: *serviceAndRegistryReadWrite*. Em **permissões**, selecione **conexão de serviço** e **gravação de registro** (a**leitura do registro** é selecionada automaticamente quando você seleciona **gravação do registro**). Em seguida, selecione **Criar**.

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. De volta ao painel **políticas de acesso compartilhado** , selecione sua nova política na lista de políticas.

6. Em **Chaves de acesso compartilhado**, selecione o ícone de cópia da **Cadeia de conexão – Chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Para obter mais informações sobre permissões e políticas de acesso compartilhado do Hub IoT, consulte [Controle de acesso e permissões](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades do dispositivo gêmeo

Nesta seção, você cria um aplicativo de console do Python que inicia um **lockDoor** remoto em um dispositivo usando um método direto e também atualiza as propriedades desejadas do dispositivo.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iot-hub**:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Usando um editor de texto, crie um novo arquivo **scheduleJobService.py** no diretório de trabalho.

3. Adicione as seguintes `import` instruções e variáveis ao início do arquivo **scheduleJobService.py** . Substitua o `{IoTHubConnectionString}` espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string). Substitua o `{deviceId}` espaço reservado pela ID do dispositivo que você registrou em [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub):

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Adicione a seguinte função que é usada para consultar dispositivos:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Adicione os seguintes métodos para executar os trabalhos que chamam o método direto e dispositivo gêmeo:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Adicione o código a seguir para agendar os trabalhos e atualizar o status do trabalho. Também inclua a rotina `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Salve e feche o arquivo **scheduleJobService.py**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando do seu diretório de trabalho, execute o seguinte comando para começar a escutar o método direto de reinicialização:

    ```cmd/sh
    python simDevice.py
    ```

2. Em outro prompt de comando no seu diretório de trabalho, execute o seguinte comando para disparar os trabalhos para bloquear a porta e atualizar o gêmeo:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Veja as respostas de dispositivo para o método direto e dispositivos gêmeos na atualização no console.

    ![Exemplo 1 de trabalho do Hub IoT – saída do dispositivo](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Exemplo de trabalho do Hub IoT 2--saída do dispositivo](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

Para continuar com a introdução ao Hub IoT e os padrões de gerenciamento de dispositivos como atualização de firmware remota aérea, veja [Como fazer uma atualização de firmware](tutorial-firmware-update.md).
