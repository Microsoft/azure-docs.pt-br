---
title: Tutorial – Desenvolver módulos em C para o Windows usando o Azure IoT Edge
description: Este tutorial mostra como criar módulos do IoT Edge com código C e implantá-los em dispositivos Windows que executam o IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a67bf92dbee9bf3d444add3be32544e4c3eba4f3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221595"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Desenvolver módulos do IoT Edge em C usando contêineres do Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo mostra como usar o Visual Studio para desenvolver código em C e implantá-lo em um dispositivo Windows que executa o Azure IoT Edge.

>[!NOTE]
>O IoT Edge 1.1 LTS é o último canal de lançamento que dá suporte a contêineres do Windows. Começando na versão 1.2, os contêineres do Windows deixarão de ter suporte. Considere usar ou mudar para o [IoT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md) para executar o IoT Edge em dispositivos Windows.

Use módulos do Azure IoT Edge para implantar um código que implementa sua lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Usar o Visual Studio para criar um módulo do IoT Edge baseado no SDK do C.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. O módulo envia mensagens upstream somente quando a temperatura ultrapassa um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em C usando o Visual Studio 2019 e implantá-lo em um dispositivo Windows. Se estiver desenvolvendo módulos usando contêineres Linux, acesse [Desenvolver módulos do IoT Edge em C usando contêineres Linux](tutorial-csharp-module.md).

Para entender as opções para desenvolver e implantar módulos em C usando contêineres Windows, confira esta tabela:

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;e&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Desenvolver módulos em C para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

Antes de começar o tutorial, configure o ambiente de desenvolvimento seguindo as instruções no tutorial [Desenvolver módulos do IoT Edge usando contêineres Windows](tutorial-develop-for-windows.md). Quando você terminar, o ambiente terá os seguintes pré-requisitos:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada padrão no Azure.
* Um [dispositivo Windows que executa o Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* O [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurado para executar contêineres do Windows.

Instale o SDK do Azure IoT C para Windows x64 por meio de vcpkg executando os seguintes comandos:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Se estiver usando o Visual Studio 2017 (versão 15.7 ou superior), baixe e instale o Azure IoT Edge Tools para o Visual Studio 2017 no [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Nesta seção, você cria um projeto de módulo do IoT Edge baseado no SDK do C usando o Visual Studio e a extensão Azure IoT Edge Tools. Depois de criar um modelo de projeto, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de C que possa ser personalizado com seu próprio código.

1. Abra o Visual Studio 2019 e selecione **Criar projeto**.

1. No painel **Criar um projeto**, pesquise **IoT Edge** e, na lista de resultados, selecione o projeto **Azure IoT Edge (Windows amd64)** .

   ![Captura de tela do painel "Criar um projeto" no IoT Edge.](./media/tutorial-c-module-windows/new-project.png)

1. Selecione **Avançar**.

    O painel **Configurar o novo projeto** é aberto.

   ![Captura de tela do painel "Configurar o novo projeto".](./media/tutorial-c-module-windows/configure-project.png)

1. No painel **Configurar o novo projeto**, renomeie o projeto e a solução para algo mais descritivo, como **CTutorialApp**. 

1. Selecione **Criar** para criar o cluster.

   O painel **Adicionar módulo** é aberto.

   ![Captura de tela do painel "Adicionar módulo" para configurar o projeto.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Na página **Configurar o novo projeto**, faça o seguinte:

   a. No painel esquerdo, selecione o modelo **Módulo C**.  
   b. Na caixa **Nome do módulo**, digite **CSharpModule**.  
   c. Na caixa **URL do Repositório**, substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure no seguinte formato: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é populada previamente com base no valor do nome do projeto do módulo.  Você pode recuperar o servidor de logon da página de visão geral do seu registro de contêiner no portal do Azure.

1. Selecione **Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O manifesto de implantação compartilha as credenciais para seu registro de contêiner com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **Chaves de acesso** do Registro de Contêiner do Azure.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo *deployment.template.json*.

1. Localize a propriedade **registryCredentials** nas propriedades desejadas do $edgeAgent. O endereço do registro da propriedade deve ser preenchido automaticamente com as informações fornecidas quando você criou o projeto. Os campos de nome de usuário e senha devem conter nomes de variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o arquivo do ambiente (ENV) em sua solução de módulo. Por padrão, o arquivo fica oculto no Gerenciador de Soluções, de modo que pode ser necessário selecionar o botão **Mostrar Todos os Arquivos** para exibi-lo. O arquivo ENV deve conter as mesmas variáveis de nome de usuário e senha que você viu no arquivo *deployment.template.json*.

1. Adicione os valores de **Nome de usuário** e **Senha** de seu Registro de Contêiner do Azure.

1. Salve suas alterações no arquivo ENV.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

O código padrão do módulo recebe mensagens em uma fila de entrada e as passa adiante por meio de sua fila de saída. Vamos adicionar mais código para que o módulo processe as mensagens na borda antes de encaminhá-las ao seu hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e envie a mensagem ao hub IoT apenas se a temperatura exceder um determinado limite.

1. Os dados do sensor nesse cenário são fornecidos no formato JSON. Para filtrar as mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza Parson.

   a. Baixe o [repositório do GitHub Parson](https://github.com/kgabis/parson).  
   b. Copie os arquivos *parson.c* e *parson.h* para o projeto CModule.  
   c. No Visual Studio, abra o arquivo *CMakeLists.txt* na pasta do projeto CModule.  
   d. Na parte superior do arquivo, importe os arquivos Parson como uma biblioteca chamada **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Adicione `my_parson` à lista de bibliotecas na seção "target_link_libraries" do arquivo *CMakeLists.txt*.  
   f. Salve o arquivo *CMakeLists.txt*.  
   g. Selecione **CModule** > **main.c**. No final da lista onde são incluídas as instruções, adicione uma instrução para incluir `parson.h` no suporte a JSON:

      ```c
      #include "parson.h"
      ```

1. No arquivo *main.c*, adicione uma variável global chamada `temperatureThreshold` ao lado da variável `messagesReceivedByInput1Queue`. Essa variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados ao hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Localize a função `CreateMessageInstance` em *main.c*. Substitua a instrução *if-else* interna pelo seguinte código, que adiciona algumas linhas de funcionalidade:

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

   As novas linhas de código na instrução *else* adicionam uma propriedade à mensagem, que a rotula como um alerta. Esse código rotula todas as mensagens como alertas porque adicionaremos uma funcionalidade que só enviará mensagens ao hub IoT se relatarem altas temperaturas.

1. Localize a função `InputQueue1Callback` e substitua toda ela pelo seguinte código. Essa função implementa o filtro de mensagens real. Quando uma mensagem é recebida, ela verifica se a temperatura relatada excede o limite. Se a temperatura exceder o limite, a função encaminhará a mensagem por meio de sua fila de saída. Se a temperatura não exceder o limite, a função ignorará a mensagem.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos têm o próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

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

1. Procure a função `SetupCallbacksForModule`. Substitua a função pelo código a seguir, que adiciona uma instrução *else-if* para verificar se o módulo gêmeo foi atualizado.

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

1. Salve o arquivo *main.c*.

1. Abra o arquivo *deployment.template.json*.

1. Adicione o módulo gêmeo **CModule** ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Captura de tela mostrando o módulo gêmeo sendo adicionado ao modelo de implantação.](./media/tutorial-c-module-windows/module-twin.png)

1. Salve o arquivo *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução do IoT Edge e adicionou um código a **CModule** para filtrar mensagens em que a temperatura relatada da máquina está abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça suas credenciais do registro de contêiner para o Docker no computador de desenvolvimento, de modo que ele possa efetuar push da imagem de contêiner para que ela seja armazenada no registro.

1. Abra o PowerShell ou uma janela do prompt de comando.

1. Entre no Docker com as credenciais do Registro de Contêiner do Azure que você salvou após criar o registro.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você poderá receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora recomendemos fazer isso como uma melhor prática para cenários de produção, essa prática está fora do escopo deste tutorial. Para saber mais, confira a [referência de logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilar e efetuar push

Agora, o computador de desenvolvimento tem acesso ao registro de contêiner e seus dispositivos IoT Edge também terão. É hora de transformar o código do projeto em uma imagem de contêiner.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto que deseja compilar. O nome padrão é **AzureIotEdgeApp1**. Para este tutorial, escolhemos o nome **CTutorialApp** e, uma vez que você está compilando um módulo do Windows, a extensão deve ser **Windows.Amd64**.

1. Selecione **Compilar e Efetuar Push de Módulos do IoT Edge**.

   O comando de build e push inicia três operações:
   * Primeiro, ele cria uma pasta na solução chamada *config*, que mantém o manifesto de implantação completo. Ele é criado com base nas informações do modelo de implantação e em outros arquivos da solução. 
   * Depois, ele executa `docker build` para compilar a imagem de contêiner com base no Dockerfile apropriado para sua arquitetura de destino. 
   * Por fim, ele executa `docker push` para efetuar push do repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-the-device"></a>Implantar módulos no dispositivo

Use o Cloud Explorer do Visual Studio e a extensão Azure IoT Edge Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já preparou um manifesto de implantação para seu cenário, o arquivo *deployment.windows-amd64.json*, na pasta *config*. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No Cloud Explorer do Visual Studio, expanda os recursos para ver sua lista de dispositivos IoT.

1. Clique com o botão direito do mouse no nome do dispositivo IoT Edge no qual você deseja receber a implantação.

1. Selecione **Criar Implantação**.

1. No Explorador de Arquivos do Visual Studio, selecione o arquivo *deployment.windows-amd64.json* na pasta *config* de sua solução.

1. Atualize o Cloud Explorer para ver os módulos implantados listados em seu dispositivo.

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo, mas não incluídos no manifesto de implantação, são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

É possível usar a extensão de Ferramentas do IoT Edge para ver as mensagens conforme elas chegam ao hub IoT.

1. No Cloud Explorer do Visual Studio, selecione o nome do seu dispositivo IoT Edge.

1. Na lista **Ações**, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

1. Veja as mensagens que estão chegando em seu hub IoT. Pode levar algum tempo para as mensagens chegarem, porque o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. As alterações no código CModule precisam aguardar até que a temperatura do computador atinja 25 graus antes que as mensagens sejam enviadas. O código também adiciona o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

   ![Captura de tela da Janela de Saída exibindo as mensagens que estão chegando ao hub IoT.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Você usou o módulo gêmeo CModule para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo *deployment.windows-amd64.json*. 

   *Não* abra o arquivo *deployment.template*. Se não vir o manifesto de implantação no arquivo *config* no Gerenciador de Soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do Gerenciador de Soluções.

1. Procure pelo CModule gêmeo e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 a 10 graus mais alta do que a última temperatura relatada.

1. Salve o arquivo *deployment.windows-amd64.json*.

1. Siga as instruções de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo.

1. Monitore as mensagens do dispositivo para nuvem recebidas. As mensagens devem parar até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar para o próximo artigo recomendado, pode manter e reutilizar os recursos e as configurações criados neste tutorial. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, para evitar encargos, você pode excluir as configurações locais e os recursos do Azure usados aqui.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge.

Para saber como o Azure IoT Edge pode ajudar você a implantar os Serviços de Nuvem do Azure para processar e analisar dados na borda, prossiga para os próximos tutoriais.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
