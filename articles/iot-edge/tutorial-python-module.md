---
title: Tutorial – Tutorial do módulo Python personalizado usando o Azure IoT Edge
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Python e implantá-lo em um dispositivo de borda.
services: iot-edge
author: kgremban
manager: philmea
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d6a95dae91ef3e6aa7d39cf8af51c355a87ea73a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462721"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Python usando contêineres Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use o Visual Studio Code para desenvolver código em Python e implantá-lo em um dispositivo que executa o Azure IoT Edge.

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o conduz pela criação e implantação de um módulo do IoT Edge que filtra os dados de sensor no dispositivo do IoT Edge que você configurou no início rápido. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Usar o Visual Studio Code para criar um módulo do IoT Edge Python.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **Python** usando o **Visual Studio Code** e como implantá-lo em um dispositivo IoT Edge.

O IoT Edge não dá suporte a módulos Python usando contêineres Windows.

Use a seguinte tabela para entender as opções para desenvolver e implantar módulos do Python usando contêineres Linux:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use o VS Code para módulos do Python no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use o VS Code para módulos do Python no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Antes de iniciar este tutorial, você deve ter percorrido o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contêiner Linux: [Desenvolver módulos do IoT Edge usando contêineres Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo que executa o Azure IoT Edge. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge em Python, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).

>[!Note]
>Verifique se a sua pasta `bin` está dentro do caminho para sua plataforma. Normalmente, `~/.local/` para UNIX e macOS ou `%APPDATA%\Python` no Windows.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um módulo Python do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução do Python que possa ser personalizado com seu próprio código.

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga os prompts e forneça as seguintes informações para criar sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo de Python**. |
   | Fornecer um nome de módulo | Nomeie seu módulo **PythonModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagens final se parece com \<registry name\>.azurecr.io/pythonmodule. |

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o repositório de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo **.env**.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo .env.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos do Python para dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Cada modelo inclui código de exemplo que usa dados simulados de sensor do módulo **SimulatedTemperatureSensor** e os encaminha ao hub IoT. Nesta seção, adicione o código que expande o **PythonModule** para analisar as mensagens antes de enviá-las.

1. No explorador do VS Code, abra **modules** > **PythonModule** > **main.py**.

2. No início do arquivo **main.py**, importe a biblioteca **json**:

    ```python
    import json
    ```

3. Adicione as variáveis **TEMPERATURE_THRESHOLD** e **TWIN_CALLBACKS** nos contadores globais. O limite de temperatura define o valor que a temperatura da máquina medida deve exceder para que os dados sejam enviados ao hub IoT.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Substitua a função **input1_listener** pelo seguinte código:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Atualize os **ouvintes** para que também ouçam atualizações de gêmeos.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Salve o arquivo main.py.

7. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge.

8. Adicione o módulo gêmeo **PythonModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **moduleContent**, após o módulo gêmeo do **$edgeHub**:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-python-module/module-twin.png)

9. Salve o arquivo deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código ao PythonModule que filtrará mensagens em que a temperatura relatada do computador estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal**.

2. Entre no Docker inserindo o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência de [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

3. No gerenciador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Criar e Efetuar Push da Solução IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o gerenciador do Visual Studio Code e a extensão Azure IoT Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.amd64.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No gerenciador do Visual Studio Code, na seção **Hub IoT do Azure**, expanda **Dispositivos** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**.

3. Selecione o arquivo **deployment.amd64.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json.

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver o novo **PythonModule** sendo executado junto com o módulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução.

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo IoT Edge e escolha **Iniciar o monitoramento de pontos de extremidade internos de eventos**.

2. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar algum tempo para que as mensagens cheguem. O dispositivo do IoT Edge precisa receber a nova implantação dele e iniciar todos os módulos. Em seguida, as alterações que fizemos no código PythonModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Também é adicionado o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo PythonModule no manifesto de implantação para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução.

2. Clique com o botão direito do mouse em **PythonModule** e selecione **Editar módulo gêmeo**.

3. Encontre **TemperatureThreshold** nas propriedades desejadas. Altere o valor para uma temperatura 5 a 10 graus maior do que a temperatura relatada mais recente.

4. Salve o arquivo de módulo gêmeo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo gêmeo e selecione **Atualizar módulo gêmeo**.

6. Monitore as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge.

É possível passar para os próximos tutoriais para saber como o Azure IoT Edge pode ajudar você a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)