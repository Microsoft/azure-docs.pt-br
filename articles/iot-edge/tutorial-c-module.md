---
title: Tutorial para desenvolver um módulo em C para Linux – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C e implantá-lo em um dispositivo Linux que está executando o IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3778361d066543325672f37cf163e58329f04abe
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221561"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Tutorial: Desenvolver um módulo do IoT Edge em C usando contêineres Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use o Visual Studio Code para desenvolver código em C e implantá-lo em um dispositivo que executa o Azure IoT Edge.

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Usar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la em um registro de contêiner
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **C** usando o **Visual Studio Code** e como implantá-lo em um dispositivo IoT Edge. Se estiver desenvolvendo módulos usando contêineres Windows, acesse [Desenvolver módulos do IoT Edge em C usando contêineres Windows](tutorial-c-module-windows.md).

Use a seguinte tabela para entender as opções para desenvolver e implantar módulos de C usando contêineres Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Usar o VS Code para módulos em C no Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Usar o VS para módulos em C no Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Usar o VS Code para módulos em C no Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Usar o VS para módulos em C no Linux ARM32](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, você deve ter percorrido o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contêiner Linux: [Desenvolver módulos do IoT Edge usando contêineres Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo que executa o Azure IoT Edge com contêineres Linux. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge em C, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* [Extensão do C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para Visual Studio Code.

A instalação do SDK do C do IoT do Azure não é necessária para este tutorial, mas pode oferecer funcionalidades úteis, como o IntelliSense e a leitura de definições de programa. Para obter informações sobre a instalação, confira [Bibliotecas e SDKs do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo do IoT Edge para C usando o Visual Studio Code e a extensão Azure IoT Tools. Quando o modelo de projeto for criado, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de C que possa ser personalizado com seu próprio código.

1. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo C**. |
   | Fornecer um nome de módulo | Nomeie o módulo **CModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br> O repositório de imagens final se parece com \<registry name\>.azurecr.io/cmodule. |

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos em C para os dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

O código padrão do módulo recebe mensagens em uma fila de entrada e as passa adiante por meio de sua fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e envie apenas a mensagem ao Hub IoT se a temperatura exceder determinado limite.

1. Os dados do sensor nesse cenário são fornecidos no formato JSON. Para filtrar as mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza Parson.

   1. Baixe o [repositório do GitHub Parson](https://github.com/kgabis/parson). Copie os arquivos **parson.c** e **parson.h** para a pasta **CModule**.

   2. Abra **modules** > **CModule** > **CMakeLists.txt**. Na parte superior do arquivo, importe os arquivos Parson como uma biblioteca chamada **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione `my_parson` à lista de bibliotecas na função **target_link_libraries** de CMakeLists.txt.

   4. Salve o arquivo **CMakeLists.txt**.

   5. Abra **modules** > **CModule** > **Main.c**. No final da lista onde são incluídas as instruções, adicione uma nova para incluir `parson.h` para suporte JSON:

      ```c
      #include "parson.h"
      ```

1. No arquivo **main.c**, adicione uma variável global chamada `temperatureThreshold` após a seção de inclusão. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Localize a função `CreateMessageInstance` em main.c. Substitua a instrução if-else interna pelo código a seguir que adiciona algumas linhas de funcionalidade:

   ```c
       if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
       {
           free(messageInstance);
           messageInstance = NULL;
       }
       else
       {
           messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
           MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
           if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
           {
              printf("ERROR: Map_AddOrUpdate Failed!\r\n");
           }
       }
   ```

   As novas linhas de código na instrução else adicionam uma nova propriedade à mensagem, que a rotula como um alerta. Esse código rotula todas as mensagens como alertas porque adicionaremos uma funcionalidade que só enviará mensagens ao Hub IoT se relatarem altas temperaturas.

1. Substitua toda a função `InputQueue1Callback` pelo seguinte código. Essa função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ela verifica se a temperatura relatada excede o limite. Em caso afirmativo, ela encaminha a mensagem por meio de sua fila de saída. Caso contrário, ela ignora a mensagem.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Localize a função `SetupCallbacksForModule`. Substitua a função pelo seguinte código que adiciona uma instrução **else if** para verificar se o módulo gêmeo foi atualizado.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Salve o arquivo main.c.

1. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge.

1. Adicione o módulo gêmeo CModule ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Adicionar CModule gêmeo ao modelo de implantação](./media/tutorial-c-module/module-twin.png)

1. Salve o arquivo **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a CModule que filtrará mensagens em que a temperatura relatada do computador estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Abra o terminal do VS Code selecionando **Exibir** > **Terminal**.

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

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver o novo **CModule** sendo executado junto com o módulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo IoT Edge e escolha **Iniciar o monitoramento de pontos de extremidade internos de eventos**.

2. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar um tempo para as mensagens chegarem, porque o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações que fizemos no código CModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Também é adicionado o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

   ![Exibir as mensagens que chegam ao Hub IoT](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo CModule no manifesto de implantação para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução.

2. Clique com o botão direito do mouse em **CModule** e selecione **Editar módulo gêmeo**.

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