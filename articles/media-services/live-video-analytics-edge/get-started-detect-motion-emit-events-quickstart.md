---
title: Introdução à Análise de Vídeo ao vivo no IoT Edge – Azure
description: Este início rápido mostra como começar a usar a Análise Dinâmica de Vídeo no IoT Edge. Saiba como detectar movimento em um fluxo de vídeo ao vivo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 57edf1721249f839f5c781756b3e09bf59888dab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730279"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Início Rápido: Introdução – Análise de Vídeo ao vivo no IoT Edge

Este guia de início rápido orienta você pelas etapas para começar a usar a Análise de Vídeo ao vivo no IoT Edge. Ele usa uma VM do Azure como um dispositivo do IoT Edge. Ele também usa um fluxo de vídeo simulado. 

Depois de concluir as etapas de instalação, você poderá executar um fluxo de vídeo ao vivo simulado por meio de um grafo de mídia que detecta e relata qualquer movimento nesse fluxo. O diagrama a seguir representa graficamente esse grafo de mídia.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Análise de Vídeo ao Vivo com base na detecção de movimento":::

Veja o seguinte vídeo com etapas detalhadas sobre como começar a usar a Análise Dinâmica de Vídeo no IoT Edge:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se não tiver uma.

  > [!NOTE]
  > Você precisará ter uma assinatura do Azure com permissões para criar entidades de serviço (a **função de proprietário** fornece isso). Caso não tenha as permissões corretas, entre em contato com o administrador da conta para conceder a você as permissões corretas.  

* O [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento. Verifique se você tem a [Extensão do Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Verifique se a rede à qual seu computador de desenvolvimento está conectado permite o AMQP (Advanced Message Queueing Protocol) pela porta 5671 para o tráfego de saída. Essa configuração permite que o Azure IoT Tools se comunique com o Hub IoT do Azure.

> [!TIP]
> Talvez você receba um prompt para instalar o Docker enquanto estiver instalando a extensão do Azure IoT Tools. Fique à vontade para ignorá-lo.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Este tutorial requer os seguintes recursos do Azure:

* Hub IoT
* Conta de armazenamento
* Conta dos Serviços de Mídia do Azure
* Uma VM do Linux no Azure, com o [runtime do IoT Edge](../../iot-edge/how-to-install-iot-edge.md) instalado

Para este início rápido, recomendamos que você use o [Script de instalação de recursos da Análise Dinâmica de Vídeo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar os recursos necessários em sua assinatura do Azure. Para fazer isso, siga estas etapas:

1. Acesse o [portal do Azure](https://portal.azure.com) e selecione o ícone do Cloud Shell.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Se estiver usando Cloud Shell pela primeira vez, você receberá um prompt para selecionar uma assinatura a fim de criar uma conta de armazenamento e um compartilhamento de Arquivos do Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para suas informações de sessão do Cloud Shell. Essa conta de armazenamento é separada da conta que o script criará para usar com sua conta dos Serviços de Mídia do Azure.
1. No menu suspenso no lado esquerdo da janela do Cloud Shell, selecione **Bash** como seu ambiente.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Seletor de ambiente":::
1. Execute o comando a seguir.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Após a conclusão bem-sucedida do script, você deverá ver todos os recursos necessários em sua assinatura. Um total de 12 recursos serão configurados pelo script:
    1. **Ponto de extremidade de streaming**: isso ajudará na reprodução do ativo do AMS gravado.
    1. **Máquina virtual**: essa é uma máquina virtual que funcionará como seu dispositivo de borda.
    1. **Disco**: esse é um disco de armazenamento anexado à máquina virtual para armazenar mídia e artefatos.
    1. **Grupo de segurança de rede**: usado para filtrar o tráfego de rede entre os recursos do Azure em uma rede virtual do Azure.
    1. **Adaptador de rede**: permite que uma Máquina Virtual do Azure se comunique com a Internet, o Azure e outros recursos.
    1. **Conexão do bastion**: permite que você se conecte à máquina virtual usando o navegador e o portal do Azure.
    1. **Endereço IP público**: permite que os recursos do Azure se comuniquem com a Internet e os serviços do Azure voltados ao público
    1. **Rede virtual**: permite a comunicação segura mútua de muitos tipos de recursos do Azure, como a sua máquina virtual, bem como com a Internet e as redes locais. Saiba mais sobre [Redes virtuais](../../virtual-network/virtual-networks-overview.md).
    1. **Hub IoT**: funciona como um hub central de mensagens para comunicação bidirecional entre o aplicativo de IoT, os módulos do IoT Edge e os dispositivos gerenciados por ele.
    1. **Conta do serviço de mídia**: ajuda a gerenciar e transmitir o conteúdo de mídia no Azure.
    1. **Conta de armazenamento**: você precisa ter uma conta de armazenamento Primária e pode ter quantas contas de armazenamento Secundárias quiser associadas à sua conta dos Serviços de Mídia. Para obter mais informações, confira [Contas do Armazenamento do Azure com as contas dos Serviços de Mídia do Azure](../latest/storage-account-concept.md).
    1. **Registro de contêiner**: ajuda a armazenar e gerenciar suas imagens particulares de contêiner do Docker e artefatos relacionados.

Na saída do script, uma tabela de recursos lista o nome do hub IoT. Procure o tipo de recurso **`Microsoft.Devices/IotHubs`** e anote o nome. Você precisará desse nome para a próxima etapa.  

> [!NOTE]
> O script também gera alguns arquivos de configuração no diretório ***~/clouddrive/lva-sample/*** . Você precisará desses arquivos posteriormente neste início rápido.

> [!TIP]
> Se você tiver problemas com os recursos do Azure que são criados, confira nosso **[guia de solução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas encontrados com frequência.

## <a name="deploy-modules-on-your-edge-device"></a>Implantar módulos no dispositivo de borda

Execute o comando a seguir no Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Esse comando implanta os seguintes módulos no dispositivo de borda, que é a VM do Linux nesse caso.

* Análise Dinâmica de Vídeo no IoT Edge (nome do módulo `lvaEdge`)
* Simulador RTSP (Real-Time Streaming Protocol) (nome do módulo `rtspsim`)

O módulo do simulador RTSP simula um fluxo de vídeo ao vivo usando um arquivo de vídeo que foi copiado para o dispositivo de borda quando você executou o [script de instalação de recursos da Análise Dinâmica de Vídeo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Agora, os módulos estão implantados, mas nenhum grafo de mídia está ativo.

## <a name="configure-the-azure-iot-tools-extension"></a>Configurar a extensão do Azure IoT Tools

Siga estas instruções para se conectar ao seu hub IoT usando a extensão do Azure IoT Tools.

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione CTRL + SHIFT + X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar Mensagem Detalhada":::
1. Selecione **Exibir** > **Gerenciador**. Ou, então, escolha CTRL + SHIFT + E.
1. No canto inferior esquerdo da guia **Explorer**, selecione **Hub IoT do Azure**.
1. Selecione o ícone **Mais opções** para ver o menu de contexto. Em seguida, selecione **Definir cadeia de conexão do Hub IoT**.
1. Quando uma caixa de diálogo de entrada aparecer, insira a cadeia de conexão do Hub IoT. No Cloud Shell, você pode obter a cadeia de conexão em *~/clouddrive/lva-sample/appsettings.json*.

> [!NOTE]
> Talvez você precise fornecer informações do ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será algo parecido com isto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Se a conexão for bem-sucedida, a lista de dispositivos de borda será mostrada. Deve haver pelo menos um dispositivo chamado **lva-sample-device**. Agora, você pode gerenciar seus dispositivos do IoT Edge e interagir com o Hub IoT do Azure por meio do menu de contexto. Para exibir os módulos implantados no dispositivo de borda, em **lva-sample-device**, expanda o nó **Módulos**.

![Nó lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Se você tiver [implantado manualmente a Análise Dinâmica de Vídeo no IoT Edge](deploy-iot-edge-device.md) por conta própria em um dispositivo de borda (como um ARM64), verá o módulo aparecer nesse dispositivo no Hub IoT do Azure. Você pode selecionar esse módulo e seguir o restante das etapas abaixo.

## <a name="use-direct-method-calls"></a>Usar chamadas de método direto

Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando os métodos diretos dele. Para obter mais informações, confira [Métodos diretos para a Análise Dinâmica de Vídeo no IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Invocar GraphTopologyList

Para enumerar todas as [topologias de grafo](media-graph-concept.md#media-graph-topologies-and-instances) no módulo:

1. No Visual Studio Code, clique com o botão direito do mouse no módulo **lvaEdge** e selecione **Invocar Método Direto do Módulo**.
1. Na caixa que aparece, digite *GraphTopologyList*.
1. Copie o conteúdo JSON a seguir e cole-o na caixa. Em seguida, pressione Enter.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Em poucos segundos, a janela **SAÍDA** mostrará a seguinte resposta.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Essa resposta é esperada pois nenhuma topologia de grafo foi criada.
    

### <a name="invoke-graphtopologyset"></a>Invocar GraphTopologySet

Como fizemos antes, agora você pode invocar `GraphTopologySet` para definir uma [topologia de grafo](media-graph-concept.md#media-graph-topologies-and-instances). Use o JSON a seguir como conteúdo.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Esse conteúdo JSON cria uma topologia de grafo que define três parâmetros. Dois desses parâmetros têm valores padrão. A topologia tem um nó de origem (origem RTSP), um nó de processador (processador de detecção de movimento) e um nó de coletor (coletor do Hub IoT).

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

O status retornado é 201. Esse status indica que uma nova topologia foi criada. 

Experimente estas próximas etapas:

1. Invoque `GraphTopologySet` novamente. O código de status retornado é 200. Esse código indica que uma topologia existente foi atualizada com êxito.
1. Invoque `GraphTopologySet` novamente, mas altere a cadeia de caracteres de descrição. O código de status retornado é 200 e a descrição é atualizada para o novo valor.
1. Invoque `GraphTopologyList` conforme descrito na seção anterior. Agora, você pode ver a topologia de `MotionDetection` no conteúdo retornado.

### <a name="invoke-graphtopologyget"></a>Invocar GraphTopologyGet

Invoque `GraphTopologyGet` usando o seguinte conteúdo.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

No conteúdo de resposta, observe estes detalhes:

* O código de status é 200, indicando êxito.
* O conteúdo inclui o carimbo de data/hora `created` e o carimbo de data/hora `lastModified`.

### <a name="invoke-graphinstanceset"></a>Invocar GraphInstanceSet

Crie uma instância de grafo que faça referência à topologia do grafo anterior. As instâncias de grafo permitem analisar fluxos de vídeo ao vivo de muitas câmeras usando a mesma topologia de grafo. Para obter mais informações, confira [Topologias e instâncias de grafo de mídia](media-graph-concept.md#media-graph-topologies-and-instances).

Invoque o método direto `GraphInstanceSet` usando o conteúdo a seguir.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Observe que esse conteúdo:

* Especifica o nome da topologia (`MotionDetection`) para a qual a instância precisa ser criada.
* Contém um valor de parâmetro para `rtspUrl`, que não tinha um valor padrão no conteúdo da topologia do grafo. Esse valor é um link para o vídeo de exemplo abaixo:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

No conteúdo de resposta, observe que:

* O código de status é 201, indicando que uma instância foi criada.
* O estado é `Inactive`, indicando que a instância do grafo foi criada, mas não ativada. Para obter mais informações, confira [Estados de grafo de mídia](media-graph-concept.md).

Experimente estas próximas etapas:

1. Invoque `GraphInstanceSet` novamente usando o mesmo conteúdo. Observe que o código de status retornado é 200.
1. Invoque `GraphInstanceSet` novamente, mas use uma descrição diferente. Observe a descrição atualizada no conteúdo da resposta, indicando que a instância do grafo foi atualizada com êxito.
1. Invoque `GraphInstanceSet`, mas altere o nome para `Sample-Graph-2`. No conteúdo de resposta, observe a instância de grafo recém-criada (ou seja, o código de status 201).

### <a name="invoke-graphinstanceactivate"></a>Invocar GraphInstanceActivate

Agora, ative a instância do grafo para iniciar o fluxo de vídeo ao vivo por meio do módulo. Invoque o método direto `GraphInstanceActivate` usando o conteúdo a seguir.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status 200 indica que a instância do grafo foi ativada com êxito.

### <a name="invoke-graphinstanceget"></a>Invocar GraphInstanceGet

Agora, invoque o método direto `GraphInstanceGet` usando o conteúdo a seguir.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

No conteúdo de resposta, observe os seguintes detalhes:

* O código de status é 200, indicando êxito.
* O estado é `Active`, indicando que a instância do grafo agora está ativa.

## <a name="observe-results"></a>Observar os resultados

A instância do grafo que nós criamos e ativamos usa o nó do processador de detecção de movimento para detectar o movimento no fluxo de vídeo ao vivo de entrada. Ela envia eventos para o nó do coletor do Hub IoT. Esses eventos são retransmitidos para Hub do IoT Edge. 

Para observar os resultados, siga estas etapas.

1. No Visual Studio Code, abra o painel do **Explorer**. No canto inferior esquerdo, procure pelo **Hub IoT do Azure**.
2. Expanda o nó **Dispositivos**.
3. Clique com o botão direito do mouse em **lva-sample-device** e selecione **Iniciar Monitoramento de Ponto de Extremidade Interno**.

    ![Iniciar o monitoramento de eventos do Hub IoT](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
A janela **SAÍDA** exibe a mensagem a seguir:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

Observe estes detalhes:

* A mensagem contém uma seção `body` e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).
* Em `applicationProperties`, `subject` faz referência ao nó do `MediaGraph` no qual a mensagem foi gerada. Nesse caso, a mensagem é originada do processador de detecção de movimento.
* Em `applicationProperties`, `eventType` indica que este é um evento de análise.
* O valor `eventTime` é a hora em que o evento ocorreu.
* A seção `body` contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém dados de `timestamp` e `inferences`.
* A seção `inferences` indica que o `type` é `motion`. Ela fornece dados adicionais sobre o evento `motion`.

Se você deixar o grafo de mídia ser executado por algum tempo, verá a seguinte mensagem na janela **SAÍDA**.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Nesta mensagem, observe os seguintes detalhes:

* Em `applicationProperties`, `subject` indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O `body` contém dados sobre o evento de diagnóstico. Nesse caso, a mensagem contém o corpo porque o evento é `MediaSessionEstablished`.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Invocar métodos diretos adicionais para limpeza

Invoque métodos diretos para, primeiro, desativar a instância do grafo e, em seguida, excluí-la.

### <a name="invoke-graphinstancedeactivate"></a>Invocar GraphInstanceDeactivate

Invoque o método direto `GraphInstanceDeactivate` usando o conteúdo a seguir.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

O código de status 200 indica que a instância do grafo foi desativada com êxito.

Em seguida, tente invocar `GraphInstanceGet` conforme indicado anteriormente neste artigo. Observe o valor de `state`.

### <a name="invoke-graphinstancedelete"></a>Invocar GraphInstanceDelete

Invoque o método direto `GraphInstanceDelete` usando o conteúdo a seguir.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Um código de status 200 indica que a instância do grafo foi excluída com êxito.

### <a name="invoke-graphtopologydelete"></a>Invocar GraphTopologyDelete

Invoque o método direto `GraphTopologyDelete` usando o conteúdo a seguir.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Em poucos segundos, você verá a seguinte resposta na janela **SAÍDA**.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Um código de status de 200 indica que a topologia de grafo foi excluída com êxito.

Experimente estas próximas etapas:

1. Invoque `GraphTopologyList` e observe que o módulo não contém topologias de grafo.
1. Invoque `GraphInstanceList` usando o mesmo conteúdo que `GraphTopologyList`. Observe que nenhuma instância do grafo é enumerada.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não vai continuar usando este aplicativo, exclua os recursos criados neste início rápido.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [gravar vídeo usando a Análise Dinâmica de Vídeo no IoT Edge](continuous-video-recording-tutorial.md).
* Saiba mais sobre as [mensagens de diagnóstico](monitoring-logging.md).