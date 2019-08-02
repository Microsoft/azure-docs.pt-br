---
title: Tutorial para desenvolver um módulo em C para Windows – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C e implantá-lo em um dispositivo Windows que está executando o IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3573e136c1a830cd75aba0725b4bf087bcd63869
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485970"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo do IoT Edge em C para dispositivos Windows

Use o Visual Studio para desenvolver código em C e implantá-lo em um dispositivo Windows que está executando o Azure IoT Edge. 

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Usar o Visual Studio para criar um módulo do IoT Edge baseado no SDK do C.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo em **C** usando o **Visual Studio 2019** e como implantá-lo em um **dispositivo Windows**. Se você estiver desenvolvendo módulos para dispositivos Linux, acesse [Desenvolver um módulo do IoT Edge em C para dispositivos Linux](tutorial-c-module.md). 

Use a tabela a seguir para entender as opções para desenvolver e implantar módulos do C em dispositivos Windows: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Desenvolver módulos em C para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Windows: [Desenvolver módulos do IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md). Após concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um [dispositivo Windows que executa o Azure IoT Edge](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Windows.
* Instale o SDK do Azure IoT C para Windows x64 por meio de vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Se estiver usando o Visual Studio 2017 (versão 15.7 ou superior), baixe e instale o [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 no Visual Studio Marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo do IoT Edge baseado no SDK do C usando o Visual Studio e a extensão Azure IoT Edge Tools. Quando o modelo de projeto for criado, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de C que possa ser personalizado com seu próprio código.

1. Inicie o Visual Studio 2019 e selecione **Criar Projeto**.

2. Na janela do novo projeto, pesquise o projeto **IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64)** . Clique em **Próximo**. 

   ![Criar um projeto do Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Na janela Configurar o novo projeto, renomeie o projeto e a solução para algo descritivo como **CTutorialApp**. Clique em **Criar** para criar o projeto. 

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. No aplicativo IoT Edge e na janela do módulo, configure seu projeto com os seguintes valores: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecione um modelo | Selecione **Módulo em C**. | 
   | Nome do projeto de módulo | Nomeie o módulo **CModule**. | 
   | Repositório de imagens do Docker | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é populada previamente com base no valor de nome do projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do Registro\>.azurecr.io/cmodule. |

   ![Configurar seu projeto para o dispositivo de destino, tipo de módulo e registro de contêiner](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Selecione **OK** para aplicar suas alterações. 

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O manifesto de implantação compartilha as credenciais para seu registro de contêiner com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **Chaves de acesso** do Registro de Contêiner do Azure. 

1. No gerenciador de soluções do Visual Studio, abra o arquivo **deployment.template.json**. 

2. Localize a propriedade **registryCredentials** nas propriedades desejadas do $edgeAgent. 

3. Atualize a propriedade com suas credenciais, seguindo este formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Salve o arquivo deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

O código padrão do módulo recebe mensagens em uma fila de entrada e as passa adiante por meio de sua fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e envie apenas a mensagem ao Hub IoT se a temperatura exceder determinado limite. 


1. Os dados do sensor nesse cenário são fornecidos no formato JSON. Para filtrar as mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza Parson.

   1. Baixe o [repositório do GitHub Parson](https://github.com/kgabis/parson). Copie os arquivos **parson.c** e **parson.h** para o projeto **CModule**.

   2. No Visual Studio, abra o arquivo **CMakeLists.txt** na pasta do projeto CModule. Na parte superior do arquivo, importe os arquivos Parson como uma biblioteca chamada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione **my_parson** à lista de bibliotecas na seção **target_link_libraries** do arquivo CMakeLists.txt.

   4. Salve o arquivo **CMakeLists.txt**.

   5. Abra **CModule** > **main.c**. No final da lista onde são incluídas as instruções, adicione uma nova para incluir `parson.h` para suporte JSON:

      ```c
      #include "parson.h"
      ```

2.  No arquivo **main.c**, adicione uma variável global chamada `temperatureThreshold` ao lado da variável messagesReceivedByInput1Queue. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Localize a função `CreateMessageInstance` em main.c. Substitua a instrução if-else interna pelo código a seguir que adiciona algumas linhas de funcionalidade: 

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

4. Localize a função `InputQueue1Callback` e substitua toda ela pelo seguinte código. Essa função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ela verifica se a temperatura relatada excede o limite. Em caso afirmativo, ela encaminha a mensagem por meio de sua fila de saída. Caso contrário, ela ignora a mensagem. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. Localize a função `SetupCallbacksForModule`. Substitua a função pelo seguinte código que adiciona uma instrução **else if** para verificar se o módulo gêmeo foi atualizado. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Salve o arquivo main.c.

8. Abra o arquivo **deployment.template.json**. 

9. Adicione o módulo gêmeo CModule ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Adicionar CModule gêmeo ao modelo de implantação](./media/tutorial-c-module-windows/module-twin.png)

1. Salve o arquivo **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Compilar e efetuar push do seu módulo

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **CModule** para filtrar mensagens em que a temperatura relatada do computador estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Use o seguinte comando para entrar no Docker em seu computador de desenvolvimento. Entre com o nome de usuário, senha e servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência do [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. No gerenciador de soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto que você deseja criar. O nome padrão é **AzureIotEdgeApp1**, e, uma vez que você está criando um módulo do Windows, a extensão deve ser **Windows.Amd64**. 

3. Selecione **Compilar e Efetuar Push de Módulos do IoT Edge**. 

   O comando de build e push inicia três operações. Primeiro, ele cria uma pasta na solução denominada **config** que contém manifesto de implantação, criado com base nas informações no modelo de implantação e em outros arquivos de solução. Em segundo lugar, ele executa `docker build` para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner. 

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o gerenciador de nuvem do Visual Studio e a extensão Ferramentas do Azure IoT Edge para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento. 

1. No gerenciador de nuvem do Visual Studio, expanda os recursos para ver sua lista de dispositivos IoT. 

2. Clique com o botão direito do mouse no nome do dispositivo IoT Edge no qual você deseja receber a implantação. 

3. Selecione **Criar Implantação**.

4. No explorador de arquivos, selecione o arquivo **deployment.windows-amd64** na pasta config de sua solução. 

5. Atualize o gerenciador de nuvem para ver os módulos implantados listados em seu dispositivo. 


## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados. 

É possível usar a extensão Ferramentas do IoT Edge para exibir as mensagens conforme elas chegam ao Hub IoT. 

1. No gerenciador de nuvem do Visual Studio, selecione o nome do seu dispositivo IoT Edge. 

2. Na lista **Ações**, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**. 

3. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar um tempo para as mensagens chegarem, porque o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações que fizemos no código CModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura. 

   ![Exibir as mensagens que chegam ao Hub IoT](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo CModule para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo **deployment.windows-amd64.json**. (Não o arquivo deployment.template. Se você não vir o manifesto de implantação no arquivo de configuração no gerenciador de soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do gerenciador.)

2. Localize o CModule gêmeo e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 a 10 graus mais alta do que a última temperatura relatada. 

3. Salve o arquivo **deployment.windows-amd64.json**.

4. Siga as etapas de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo. 

5. Monitorar as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando você estiver pronto para criar seus próprios módulos, poderá saber mais sobre como [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio](how-to-visual-studio-develop-module.md). É possível passar para os próximos tutoriais para saber como o Azure IoT Edge pode ajudar você a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
