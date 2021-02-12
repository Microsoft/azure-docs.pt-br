---
title: Implantar um aplicativo Web de análise espacial
titleSuffix: Azure Cognitive Services
description: Saiba como usar a análise espacial em um aplicativo Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183465"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Como implantar um aplicativo Web de contagem de pessoas

Use este artigo para aprender a integrar a análise espacial em um aplicativo Web que compreende a movimentação de pessoas e monitora o número de pessoas que ocupam um espaço físico. 

Neste tutorial, você aprenderá a:

* Implantar o contêiner de análise espacial
* Configurar a operação e a câmera
* Configurar a conexão do Hub IoT no aplicativo Web
* Implantar e testar o aplicativo Web

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Noções básicas sobre Azure IoT Edge configurações de implantação e um [Hub IOT do Azure](../../iot-hub/index.yml)
* Um [computador host](spatial-analysis-container.md)configurado.

## <a name="deploy-the-spatial-analysis-container"></a>Implantar o contêiner de análise espacial

Preencha o [aplicativo de solicitação](https://aka.ms/csgate) para obter acesso para executar o contêiner. 

Siga [a configuração do computador host](./spatial-analysis-container.md) para configurar o computador host e conectar um dispositivo IOT Edge ao Hub IOT do Azure. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Implantar um serviço de Hub IoT do Azure em sua assinatura

Primeiro, crie uma instância de um serviço de Hub IoT do Azure com o tipo de preço Standard (S1) ou a camada gratuita (S0). Siga estas instruções para criar essa instância usando o CLI do Azure.

Preencha os parâmetros necessários:
* Assinatura: o nome ou a ID da sua assinatura do Azure
* Grupo de recursos: Crie um nome para seu grupo de recursos
* Nome do Hub IOT: criar um nome para o Hub IoT
* Nome do IoTHub: o nome do Hub IoT que você criou 
* Nome do dispositivo de borda: Crie um nome para o dispositivo de borda

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Implantar o contêiner em Azure IoT Edge no computador host

Implante o contêiner de análise espacial como um módulo IoT no computador host, usando o CLI do Azure. O processo de implantação requer um arquivo de manifesto de implantação que descreve os contêineres, as variáveis e as configurações necessárias para sua implantação. Você pode encontrar um exemplo de [manifesto de implantação específico de Azure Stack de borda](https://go.microsoft.com/fwlink/?linkid=2142179), manifesto de [implantação específico de Azure Stack de borda](https://go.microsoft.com/fwlink/?linkid=2152189)e [VM do Azure com manifesto de implantação específico de GPU](https://go.microsoft.com/fwlink/?linkid=2152189) no GitHub, que incluem uma configuração de implantação básica para o contêiner de *análise espacial* . 

Como alternativa, você pode usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Vá para [implantar módulos de Azure IOT Edge de Visual Studio Code](../../iot-edge/how-to-deploy-modules-vscode.md) para saber mais.

> [!NOTE] 
> Os contêineres *Spatial-Analysis-Telegraf* e *Spatial-Analysis-Diagnostics* são opcionais. Você pode decidir removê-los do *DeploymentManifest.jsno* arquivo. Para obter mais informações, consulte o artigo [telemetria e solução de problemas](./spatial-analysis-logging.md) . Você pode encontrar três *DeploymentManifest.jsde exemplo em* arquivos no GitHub, para [dispositivos Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), um [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152189)ou uma [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A maioria das **variáveis de ambiente** para o módulo IOT Edge já está definida noDeploymentManifest.jsde exemplo *nos* arquivos vinculados acima. No arquivo, procure as variáveis de `BILLING_ENDPOINT` `API_KEY` ambiente e, mostradas abaixo. Substitua os valores com o URI do ponto de extremidade e a chave de API que você criou anteriormente. Verifique se o valor do EULA está definido como "Accept". 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Configurar os parâmetros da operação

Agora que a configuração inicial do contêiner de *análise espacial* foi concluída, a próxima etapa é configurar os parâmetros de operações e adicioná-los à implantação. 

A primeira etapa é atualizar o manifesto de implantação de exemplo vinculado acima e configurar o operationId `cognitiveservices.vision.spatialanalysis-personcount` como mostrado abaixo:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Depois que o manifesto de implantação for atualizado, siga as instruções do fabricante da câmera para instalar a câmera, configurar a URL da câmera e configurar o nome de usuário e a senha. 

Em seguida, defina `VIDEO_URL` para a URL RTSP da câmera e as credenciais para conectar-se à câmera.

Se o dispositivo de borda tiver mais de uma GPU, selecione a GPU na qual executar essa operação. Certifique-se de balancear a carga das operações em que não há mais de 8 operações em execução em uma única GPU por vez.  

Em seguida, configure a zona na qual você deseja contar as pessoas. Para configurar o polígono da zona, primeiro siga as instruções do fabricante para recuperar um quadro da câmera. Para determinar cada vértice do polígono, selecione um ponto no quadro, use as coordenadas x, y pixel do ponto em relação à esquerda, canto superior do quadro e divida pelas dimensões do quadro correspondente. Defina os resultados como coordenadas x, y do vértice. Você pode definir a configuração de polígono da zona no `SPACEANALYTICS_CONFIG` campo.

Este é um quadro de vídeo de exemplo que mostra como as coordenadas de vértice estão sendo calculadas para um quadro de tamanho 1920/1080.
![Quadro de vídeo de exemplo](./media/spatial-analysis/sample-video-frame.jpg)

Você também pode selecionar um limite de confiança para quando as pessoas detectadas são contadas e os eventos são gerados. Defina o limite como 0 se desejar que todos os eventos sejam gerados.

### <a name="execute-the-deployment"></a>Executar a implantação

Agora que o manifesto de implantação foi concluído, use esse comando no CLI do Azure para implantar o contêiner no computador host como um módulo IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Preencha os parâmetros necessários:

* Nome do Hub IoT: o nome do Hub IoT do Azure
* DeploymentManifest.jsem: o nome do seu arquivo de implantação
* Nome do dispositivo de IoT Edge: o nome do dispositivo de IoT Edge do seu computador host
* Assinatura: sua ID de assinatura ou nome

Esse comando iniciará a implantação e você poderá exibir o status da implantação em sua instância do Hub IoT do Azure no portal do Azure. O status pode ser mostrado como *417 – a configuração de implantação do dispositivo não está definida* até que o dispositivo termine de baixar as imagens de contêiner e comece a ser executado.

### <a name="validate-that-the-deployment-was-successful"></a>Validar que a implantação foi bem-sucedida

Localize o *status de tempo de execução* nas configurações do módulo IOT Edge para o módulo de análise espacial em sua instância do Hub IoT na portal do Azure. O **valor desejado** e o **valor relatado** para o *status de tempo de execução* devem dizer `Running` . Veja abaixo o que será a aparência no portal do Azure.

![Exemplo de verificação de implantação](./media/spatial-analysis/deployment-verification.png)

Neste ponto, o contêiner de análise espacial está executando a operação. Ele emite informações de ia para a `cognitiveservices.vision.spatialanalysis-personcount` operação e roteia essas informações como telemetria para a instância do Hub IOT do Azure. Para configurar câmeras adicionais, você pode atualizar o arquivo de manifesto de implantação e executar a implantação novamente.

## <a name="person-counting-web-application"></a>Aplicativo Web de contagem de pessoas

Essa pessoa contando o aplicativo Web permite que você configure rapidamente um aplicativo Web de exemplo e hospede-o em seu ambiente do Azure.

### <a name="get-the-person-counting-app-container"></a>Obter o contêiner de aplicativo de contagem de pessoas

Um formulário de contêiner deste aplicativo disponível no registro de contêiner do Azure. Use o comando docker pull a seguir para baixá-lo. Entre em contato com a Microsoft em projectarchon@microsoft.com para obter o token de acesso.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Envie por push o contêiner para o seu ACR (registro de contêiner do Azure).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Para instalar o contêiner, crie um novo Aplicativo Web para Contêineres do Azure e preencha os parâmetros necessários. Em seguida, vá para a guia **Docker** e selecione **contêiner único** e, em seguida, **registro de contêiner do Azure**. Use sua instância do registro de contêiner do Azure onde você enviou por push a imagem acima.

![Inserir detalhes da imagem](./media/spatial-analysis/solution-app-create-screen.png)

Depois de inserir os parâmetros acima, clique em **examinar + criar** e crie o aplicativo.

### <a name="configure-the-app"></a>Configurar o aplicativo 

Aguarde a conclusão da instalação e navegue até o recurso no portal do Azure. Vá para a seção de **configuração** e adicione as duas **configurações de aplicativo** a seguir.

* `EventHubConsumerGroup` – O nome da cadeia de caracteres do grupo de consumidores do Hub IoT do Azure, você pode criar um novo grupo de consumidores em seu hub IoT ou usar o grupo padrão. 
* `IotHubConnectionString` – A cadeia de conexão para o Hub IoT do Azure, que pode ser recuperada na seção chaves do recurso do Hub IoT do Azure ![ configurar parâmetros](./media/spatial-analysis/solution-app-config-page.png)

Depois que essas duas configurações forem adicionadas, clique em **salvar**. Em seguida, clique em **autenticação/autorização** no menu de navegação à esquerda e atualize-o com o nível de autenticação desejado. Recomendamos Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>Testar o aplicativo

Vá para o aplicativo Web do Azure e verifique se a implantação foi bem-sucedida e se o aplicativo Web está em execução. Navegue até a URL configurada: `<yourapp>.azurewebsites.net` para exibir o aplicativo em execução.

![Teste a implantação](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Obter o código-fonte PersonCount
Se você quiser exibir ou modificar o código-fonte para este aplicativo, poderá encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Próximas etapas

* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)
