---
title: Implantar um aplicativo Web de Análise Espacial
titleSuffix: Azure Cognitive Services
description: Saiba como usar uma Análise Espacial em um aplicativo Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 6d3be90cc81b1bcd9a55fc8e53cb9f2238e8c6de
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285970"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Como implantar um aplicativo Web de Contagem de Pessoas

Use este artigo para aprender a integrar uma Análise Espacial em um aplicativo Web que compreende a movimentação de pessoas e monitora o número de pessoas que ocupam um espaço físico. 

Neste tutorial, você aprenderá a:

* Implantar o contêiner de Análise Espacial
* Configurar a operação e a câmera
* Configurar a conexão do Hub IoT no aplicativo Web
* Implantar e testar o aplicativo Web

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Ter noções básicas sobre as configurações de implantação do Azure IoT Edge e um [Hub IoT do Azure](../../iot-hub/index.yml)
* Um [computador host](spatial-analysis-container.md) configurado.

## <a name="deploy-the-spatial-analysis-container"></a>Implantar o contêiner de Análise Espacial

Preencha a opção [solicitar aplicativo](https://aka.ms/csgate) a fim de obter acesso para executar o contêiner. 

Siga a [Configuração do Computador Host](./spatial-analysis-container.md) para configurar o computador host e conectar um dispositivo do IoT Edge ao Hub IoT do Azure. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Implantar um serviço do Hub IoT do Azure em sua assinatura

Primeiro, crie uma instância de um serviço do Hub IoT do Azure com o Tipo de Preço Standard (S1) ou a Camada Gratuita (S0). Siga estas instruções para criar essa instância usando a CLI do Azure.

Preencha os parâmetros necessários:
* Assinatura: o nome ou a ID de sua assinatura do Azure
* Grupo de recursos: crie um nome para o grupo de recursos
* Nome do Hub IoT: crie um nome para o Hub IoT
* Nome do Hub IoT: o nome do Hub IoT criado 
* Nome do dispositivo de borda: crie um nome para o dispositivo de borda

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Implantar o contêiner no Azure IoT Edge do computador host

Implante o contêiner de Análise Espacial como um Módulo de IoT no computador host usando a CLI do Azure. O processo de implantação requer um arquivo de manifesto de implantação que descreverá os contêineres, as variáveis e as configurações necessários para a implantação. É possível encontrar exemplos de um [manifesto de implantação específico do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), um [manifesto de implantação específico não Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2152189) e um [manifesto de implantação específico de VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) no GitHub, que inclui uma configuração de implantação básica do contêiner *spatial-analysis*. 

Como alternativa, é possível usar extensões do Azure IoT para o Visual Studio Code a fim de executar operações com o hub IoT. Acesse o artigo [Implantar módulos do Azure IoT Edge do Visual Studio Code](../../iot-edge/how-to-deploy-modules-vscode.md) para obter mais informações.

> [!NOTE] 
> Os contêineres *spatial-analysis-telegraf* e *spatial-analysis-diagnostics* são opcionais. Você poderá decidir se deseja removê-los do arquivo *DeploymentManifest.json*. Para obter mais informações, confira o artigo sobre [telemetria e solução de problemas](./spatial-analysis-logging.md). É possível encontrar três arquivos *DeploymentManifest.json* de exemplo no GitHub para [dispositivos do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), além de um [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152189) ou uma [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A maioria das **Variáveis de Ambiente** do Módulo do IoT Edge já está definida nos arquivos *DeploymentManifest.json* de exemplo indicados acima. No arquivo, pesquise as variáveis de ambiente `BILLING_ENDPOINT` e `API_KEY` mostradas abaixo. Substitua os valores pelo URI do Ponto de Extremidade e a Chave de API criados anteriormente. Verifique se o valor dos termos de licença está definido como "accept". 

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

Agora que a configuração inicial do contêiner *spatial-analysis* foi concluída, a próxima etapa será configurar os parâmetros das operações e adicioná-los à implantação. 

A primeira etapa será atualizar o manifesto de implantação de exemplo indicado acima e configurar o operationId para a operação `cognitiveservices.vision.spatialanalysis-personcount`, conforme mostrado abaixo:


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

Após atualizar o manifesto de implantação, siga as instruções do fabricante da câmera para instalar a câmera, configurar a URL da câmera, bem como configurar o nome de usuário e a senha. 

Depois, defina `VIDEO_URL` para a URL RTSP da câmera e as credenciais para estabelecer uma conexão com a câmera.

Caso o dispositivo de borda tenha mais de uma GPU, selecione a GPU em que você deseja executar essa operação. Lembre-se de balancear carga de operações em que não há mais de oito operações em execução em uma GPU por vez.  

Depois, configure a zona em que você deseja contar pessoas. Para configurar o polígono de zona, primeiro siga as instruções do fabricante a fim de recuperar um quadro da câmera. Para determinar cada vértice do polígono, selecione um ponto no quadro, use as coordenadas de pixel x e y do ponto esquerdo no canto superior do quadro, depois divida esse valor pelas dimensões correspondentes do quadro. Defina os resultados como as coordenadas x e y do vértice. É possível definir a configuração do polígono de zona no campo `SPACEANALYTICS_CONFIG`.

Este é um quadro de vídeo de exemplo que mostrará de que modo as coordenadas do vértice estão sendo calculadas para um quadro de tamanho 1920/1080.
![Quadro de vídeo de exemplo](./media/spatial-analysis/sample-video-frame.jpg)

Também é possível selecionar um limite de confiança para ser usado durante a contagem de pessoas detectadas e a geração de eventos. Defina o limite como 0 caso deseje que todos os eventos sejam gerados.

### <a name="execute-the-deployment"></a>Executar a implantação

Agora que o manifesto de implantação foi concluído, use este comando na CLI do Azure para implantar o contêiner no computador host como um Módulo do IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Preencha os parâmetros necessários:

* Nome do Hub IoT: o nome do Hub IoT do Azure
* DeploymentManifest.json: o nome do arquivo de implantação
* Nome do dispositivo do IoT Edge: o nome do dispositivo do IoT Edge do computador host
* Assinatura: o nome ou a ID da assinatura

Esse comando iniciará a implantação. Além disso, será possível ver o status da implantação em sua instância do Hub IoT do Azure no portal do Azure. O status poderá exiba a mensagem *417 – A configuração de implantação do dispositivo não está definida* até que o dispositivo termine de baixar as imagens de contêiner e inicie a execução.

### <a name="validate-that-the-deployment-was-successful"></a>Verificar se a implantação foi concluída com êxito

Localize o *Status do Runtime* nas Configurações do Módulo do IoT Edge para verificar o módulo spatial-analysis em sua instância do Hub IoT no portal do Azure. O **Valor Desejado** e o **Valor Relatado** no *Status do Runtime* deverão exibir `Running`. Veja abaixo de que modo essas informações serão exibidas no portal do Azure.

![Verificação da implantação de exemplo](./media/spatial-analysis/deployment-verification.png)

Neste momento, o contêiner spatial-analysis executará a operação. Ele emitirá insights da IA para a operação `cognitiveservices.vision.spatialanalysis-personcount`, bem como encaminhará esses insights como dados telemétricos para sua instância do Hub IoT do Azure. É possível atualizar o arquivo de manifesto de implantação, bem como executar a implantação novamente para configurar câmeras adicionais.

## <a name="person-counting-web-application"></a>Aplicativo Web de contagem de pessoas

Este aplicativo Web de contagem de pessoas permite configurar de modo rápido um aplicativo Web de exemplo e hospedá-lo em seu ambiente do Azure.

### <a name="get-the-person-counting-app-container"></a>Obter o contêiner do aplicativo de contagem de pessoas

Um formulário do contêiner deste aplicativo está disponível no Registro de Contêiner do Azure. Use o comando docker pull abaixo para baixá-lo. Entre em contato com a Microsoft em projectarchon@microsoft.com para obter o token de acesso.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Envie o contêiner por push ao seu ACR (Registro de Contêiner do Azure).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Crie um Serviço de Aplicativo do Azure e preencha os parâmetros necessários para instalar o contêiner. Depois, acesse a guia **Docker**, selecione **Contêiner Único** e **Registro de Contêiner do Azure**. Use a instância do Registro de Contêiner do Azure em que você enviou a imagem acima por push.

![Inserir detalhes da imagem](./media/spatial-analysis/solution-app-create-screen.png)

Depois de inserir os parâmetros acima, clique em **Examinar+Criar** e crie o aplicativo.

### <a name="configure-the-app"></a>Configurar o aplicativo 

Aguarde a conclusão da instalação e acesse o recurso no portal do Azure. Navegue até a seção de **Configuração** e adicione as duas **configurações de aplicativo** abaixo.

* `EventHubConsumerGroup` – O nome da cadeia de caracteres do grupo de consumidores do Hub IoT do Azure. É possível criar um grupo de consumidores em seu Hub IoT ou usar o grupo padrão. 
* `IotHubConnectionString` – A cadeia de conexão usada para acessar o Hub IoT do Azure. Ela pode ser recuperada na seção de chaves do recurso do Hub IoT do Azure em ![Configurar Parâmetros](./media/spatial-analysis/solution-app-config-page.png)

Após adicionar essas duas configurações, clique em **Salvar**. Em seguida, clique em **Autenticação/Autorização** no menu de navegação esquerdo, depois execute uma atualização usando o nível de autenticação desejado. Recomendamos usar o Azure AD (Azure Active Directory) Express. 

### <a name="test-the-app"></a>Testar o aplicativo

Acesse o Serviço do Azure, verifique se a implantação foi concluída com êxito e se o aplicativo Web está em execução. Acesse a URL configurada: `<yourapp>.azurewebsites.net` para ver o aplicativo em execução.

![Teste a implantação](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Obter o código-fonte PersonCount
Caso queira ver ou modificar o código-fonte deste aplicativo, será possível encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Próximas etapas

* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento de câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e de linha](spatial-analysis-zone-line-placement.md)
