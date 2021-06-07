---
title: 'Início Rápido: Enviar telemetria do dispositivo ao Azure IoT Central (Python)'
description: Neste início rápido, você usa o SDK do Dispositivo do Hub IoT do Azure para Python para enviar telemetria de um dispositivo ao IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047160"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Início Rápido: Enviar telemetria de um dispositivo ao Azure IoT Central (Python)

**Aplica-se a**: [desenvolvimento de aplicativos do dispositivo](about-iot-develop.md#device-application-development)

Neste guia de início rápido, aprenda um fluxo de trabalho básico de desenvolvimento de aplicativos do dispositivo IoT. Primeiro, você usa o Azure IoT Central para criar um aplicativo em nuvem. Em seguida, você usa o SDK do Python do Azure IoT para criar um dispositivo simulado, conectar-se ao IoT Central e enviar telemetria do dispositivo para a nuvem. 

## <a name="prerequisites"></a>Pré-requisitos
- [Python 3.7+](https://www.python.org/downloads/). Para outras versões do Python com suporte, confira [Recursos do Dispositivo IoT do Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Para garantir que sua versão do Python esteja atualizada, execute `python --version`. Se o Python 2 e o Python 3 estiverem instalados e você estiver usando um ambiente do Python 3, instale todas as bibliotecas usando `pip3`. A execução de `pip3` garante que as bibliotecas sejam instaladas em seu runtime do Python 3.
    > [!IMPORTANT]
    > No instalador do Python, selecione a opção para **Adicionar Python ao PATH**. Se o Python 3.7 ou superior já estiver instalado, confirme que adicionou a pasta de instalação do Python à variável de ambiente `PATH`.

## <a name="create-an-application"></a>Criar um aplicativo
Nesta seção, você cria um aplicativo do IoT Central. O IoT Central é uma plataforma de aplicativo IoT baseada em portal que ajuda a reduzir a complexidade e o custo de desenvolvimento e gerenciamento de soluções de IoT.

Para criar um aplicativo do Azure IoT Central:
1. Navegue até o [Azure IoT Central](https://apps.azureiotcentral.com/) e entre com uma conta pessoal, corporativa ou de estudante da Microsoft.
1. Navegue até **Build** e selecione **Aplicativos personalizados**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Página inicial do IoT Central":::
1. Em **Nome do aplicativo**, insira um nome exclusivo ou use o nome gerado.
1. Em **URL**, insira um prefixo de URL de aplicativo fácil de memorizar ou use o prefixo gerado.
1. Deixe **Modelo de aplicativo** definido como *Aplicativo personalizado*. A lista suspensa poderá mostrar outras opções se já existirem modelos em sua conta.
1. Selecione uma opção de **Plano de preços**. 
    - Para usar o aplicativo gratuitamente por sete dias, selecione **Gratuito**. Você pode converter um aplicativo gratuito no preço padrão antes que ele expire.
    - Você também pode selecionar um plano de preços padrão. Se você selecionar o preço padrão, mais opções serão exibidas e você precisará definir um **Diretório**, uma **Assinatura do Azure** e um **Local**. Para saber mais sobre os preços, confira [Preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Diretório** é o Azure Active Directory no qual você criará o aplicativo. Um Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Caso você não tenha um Azure Active Directory, um será criado quando você criar uma assinatura do Azure.
        - Uma **assinatura do Azure** permite que você crie instâncias de serviços do Azure. O IoT Central provisiona recursos em sua assinatura. Se você não tem uma assinatura do Azure, pode [criar uma gratuita](https://azure.microsoft.com/free/). Após criar a assinatura do Azure, volte para a página **Novo aplicativo** do IoT Central. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.
        - O **Local** é a [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) na qual você cria um aplicativo. Selecione o local fisicamente mais próximo de seus dispositivos para ter um desempenho ideal. Depois de escolher um local, você não poderá mover o aplicativo para outro local.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Caixa de diálogo de novo aplicativo do IoT Central":::
1. Selecione **Criar**.
    
    Após criar o aplicativo, o IoT Central redireciona você para o painel do aplicativo.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="Painel de novo aplicativo do IoT Central":::

## <a name="add-a-device"></a>Adicionar um dispositivo
Nesta seção, você adiciona um novo dispositivo ao seu aplicativo do IoT Central. O dispositivo é uma instância de um modelo de dispositivo que representa um dispositivo real ou simulado que você conectará ao aplicativo. 

Para criar um dispositivo:
1. No painel esquerdo, selecione **Dispositivos** e escolha **+Novo**. Isso abre a caixa de diálogo de novo dispositivo.
1. Deixe o **Modelo de dispositivo** definido como *Não atribuído*.

    > [!NOTE]
    > Neste início rápido, para simplificar, você conecta um dispositivo simulado que usa um modelo não atribuído. Se continuar usando IoT Central para gerenciar dispositivos, você aprenderá sobre o uso de modelos de dispositivo. Para ter uma visão geral de como trabalhar com modelos de dispositivo, confira [Início Rápido: Adicionar um dispositivo simulado ao seu aplicativo IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Defina um **Nome do dispositivo** e uma **ID do Dispositivo** amigáveis. Também é possível usar os valores gerados.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Caixa de diálogo de novo dispositivo do IoT Central":::
1. Selecione **Criar**.

    O dispositivo criado aparece na lista **Todos os dispositivos**.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Lista de todos os dispositivos do IoT Central":::
    
Para recuperar detalhes da conexão para o novo dispositivo:
1. Na lista **Todos os dispositivos**, clique duas vezes no nome do dispositivo vinculado para exibir os detalhes. 
1. No menu superior, selecione **Conectar**.

    A caixa de diálogo **Conexão do dispositivo** exibe os detalhes da conexão: :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="Detalhes da conexão do dispositivo do IoT Central":::
1. Copie os valores a seguir da caixa de diálogo **Conexão do dispositivo** para um local seguro. Você os usará na próxima seção para conectar seu dispositivo ao IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Enviar mensagens e monitorar a telemetria
Nesta seção, você usará o SDK do Python para criar um dispositivo simulado e enviar telemetria ao seu aplicativo do IoT Central. 

1. Abra um terminal usando o Windows CMD, o PowerShell ou o Bash (para Windows ou Linux). Você usará o terminal para instalar o SDK do Python, atualizar variáveis de ambiente e executar o exemplo de código do Python.

1. Copie os [exemplos de dispositivo do SDK do Python do Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) para o computador local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Navegue até o diretório *azure-iot-sdk-python/azure-iot-device/samples*.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Instale o SDK do Python do Azure IoT.

    ```console
    pip install azure-iot-device
    ```

1. Defina cada uma das variáveis de ambiente a seguir para habilitar o dispositivo simulado a se conectar ao IoT Central. Para `ID_SCOPE`, `DEVICE_ID` e `DEVICE_KEY`, use os valores que você salvou da caixa de diálogo *Conexão de dispositivo* do IoT Central.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Para o Windows CMD, não há aspas ao redor da cadeia de conexão nem dos outros valores de variáveis.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. No terminal, execute o código do arquivo de exemplo *simple_send_temperature.py. Esse código acessa o dispositivo IoT simulado e envia uma mensagem ao IoT Central.

    Para executar o exemplo de Python no terminal:
    ```console
    python ./simple_send_temperature.py
    ```

    Opcionalmente, você também pode executar o código Python por meio do exemplo no seu Python IDE:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Conforme o código Python envia uma mensagem de seu dispositivo ao aplicativo do IoT Central, as mensagens aparecem na guia **Dados brutos** do dispositivo no IoT Central. Talvez seja necessário atualizar a página para mostrar as mensagens recentes.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Captura de tela da saída de dados brutos do IoT Central":::

Agora, seu dispositivo está conectado com segurança e enviando telemetria ao Azure IoT.

## <a name="clean-up-resources"></a>Limpar os recursos
Se não precisar mais dos recursos do IoT Central criados neste tutorial, você poderá excluí-los no portal do IoT Central. Como opção, se você planeja continuar seguindo a documentação neste guia, pode manter o aplicativo criado e reutilizá-lo para outros exemplos.

Para remover o aplicativo de exemplo do Azure IoT Central e todos os seus dispositivos e recursos:
1. Selecione **Administração** > **Seu aplicativo**.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu um fluxo de trabalho básico do aplicativo de IoT do Azure para conectar com segurança um dispositivo à nuvem e enviar telemetria do dispositivo para nuvem. Você usou o Azure IoT Central para criar um aplicativo e um dispositivo. Em seguida, você usou o SDK do Python do Azure IoT para criar um dispositivo simulado e enviar telemetria. Você também usou o IoT Central para monitorar a telemetria.

Como próxima etapa, explore o SDK do Python de IoT do Azure por meio de exemplos de aplicativo.

- [Exemplos Assíncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): esse diretório contém exemplos de Python assíncronos para cenários adicionais do Hub IoT.
- [Exemplos Síncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): esse diretório contém exemplos do Python para uso com o Python 2.7 ou em cenários de compatibilidade síncrona com o Python 3.6 e posterior
- [Exemplos do IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): esse diretório contém exemplos de Python para trabalhar com módulos do Edge e dispositivos downstream.
