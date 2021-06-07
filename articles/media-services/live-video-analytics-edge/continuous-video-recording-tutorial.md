---
title: Tutorial de gravação contínua de vídeo na nuvem e reprodução com origem na nuvem – Azure
description: Neste tutorial, você aprenderá a usar a Análise Dinâmica de Vídeo do Azure no Azure IoT Edge para gravar vídeo continuamente na nuvem e para transmitir qualquer parte desse vídeo usando os Serviços de Mídia do Azure.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: d534fb174afa700671b6a3991a6aafa6357eb167
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280336"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Gravação contínua de vídeo baseada na nuvem e reprodução com origem na nuvem

Neste tutorial, você aprenderá a usar a Análise Dinâmica de Vídeo do Azure no Azure IoT Edge para realizar uma [gravação contínua de vídeo](continuous-video-recording-concept.md) (CVR) na nuvem e para transmitir qualquer parte desse vídeo usando os Serviços de Mídia do Azure. Isso é útil para cenários de segurança e conformidade, bem como para outros cenários em que há necessidade de manter arquivos da gravação de uma câmera por dias ou semanas. 

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar os recursos relevantes.
> * Examinar o código que realiza a CVR.
> * Executar o código de exemplo.
> * Examinar os resultados e exibir o vídeo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia estes artigos antes de começar:

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md) 
* [Cenários de gravação de vídeo contínua](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos deste tutorial são:

* O [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com as extensões do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Pode ser solicitado que você instale o Docker. Ignore esse aviso.
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) instalado no computador de desenvolvimento.
* Conclua o [script de instalação de recursos da Análise Dinâmica de Vídeo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

No final dessas etapas, você terá os recursos do Azure relevantes implantados em sua assinatura do Azure:

* Hub IoT do Azure
* Conta de Armazenamento do Azure
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](../../iot-edge/how-to-install-iot-edge.md) instalado

> [!TIP]
> Se você tiver problemas com os recursos do Azure que são criados, confira nosso **[guia de solução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas encontrados com frequência.

## <a name="concepts"></a>Conceitos

Conforme explicado no artigo [Conceito de grafo de mídia](media-graph-concept.md), um grafo de mídia permite que você defina:

- De onde a mídia deve ser capturada.
- Como ela deve ser processada.
- Onde os resultados devem ser entregues. 
 
 Para realizar a CVR, você precisa capturar o vídeo de uma câmera compatível com RTSP e gravá-lo continuamente em um [ativo dos Serviços de Mídia do Azure](terminology.md#asset). Este diagrama mostra uma representação gráfica desse grafo de mídia.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Grafo de mídia":::

Neste tutorial, você usará um módulo do Edge criado usando o [Servidor de Mídia Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmera RTSP. Dentro do grafo de mídia, você usará um nó de [origem RTSP](media-graph-concept.md#rtsp-source) para obter o feed ao vivo e enviar esse vídeo para o [nó do coletor de ativos](media-graph-concept.md#asset-sink), que grava o vídeo em um ativo. O vídeo que será usado neste tutorial é [um vídeo de exemplo de interseção de rodovia](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Antes de começar, verifique se você concluiu o terceiro marcador dos [Pré-requisitos](#prerequisites). Após a conclusão do script de instalação, selecione as chaves para expor a estrutura de pastas. Você verá alguns arquivos criados no diretório ~/clouddrive/lva-sample.

![Configurações do aplicativo](./media/quickstarts/clouddrive.png)

Os arquivos de interesse neste tutorial são:

* **~/clouddrive/lva-sample/edge-deployment/.env**: contém propriedades que o Visual Studio Code usa para implantar módulos em um dispositivo de borda.
* **~/clouddrive/lva-sample/appsettings.json**: usado pelo Visual Studio Code para executar o código de exemplo.

Você precisará dos arquivos para estas etapas:

1. Clone o repositório do link do GitHub https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Inicie o Visual Studio Code e abra a pasta para a qual você baixou o repositório.
1. No Visual Studio Code, navegue até a pasta src/cloud-to-device-console-app e crie um arquivo chamado **appsettings.json**. Esse arquivo contém as configurações necessárias para executar o programa.
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/appsettings.json. O texto deve ter esta aparência:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    A cadeia de conexão do Hub IoT permite que você use o Visual Studio Code para enviar comandos para os módulos de borda por meio do Hub IoT do Azure.
    
1. Em seguida, navegue até a pasta src/edge e crie um arquivo chamado **.env**.
1. Copie o conteúdo do arquivo ~/clouddrive/lva-sample/edge-deployment/.env. O texto deve ter esta aparência:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Examinar os arquivos de exemplo

No Visual Studio Code, abra src/edge/deployment.template.json. Este modelo define quais módulos de borda serão implantados no dispositivo de borda (a VM Linux do Azure). Observe que há duas entradas na seção **módulos**, com os seguintes nomes:

* **lvaEdge**: é a Análise Dinâmica de Vídeo no módulo do IoT Edge.
* **rtspsim**: é o simulador RTSP.

Em seguida, navegue até a pasta src/cloud-to-device-console-app. Aqui, você verá o arquivo appsettings.json que foi criado junto com alguns outros arquivos:

* **c2d-console-app.csproj**: o arquivo de projeto do Visual Studio Code.
* **operations.json**: este arquivo lista as diferentes operações que você executaria.
* **Program.cs**: o código do programa de exemplo, que:
    * Carrega as configurações do aplicativo.
    * Invoca métodos diretos expostos pela Análise de Vídeo ao vivo no módulo do IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](direct-methods.md).
    * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos gerados pelo módulo na janela **SAÍDA**.
    * Invoca métodos diretos para limpar recursos.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge 

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e as definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.

1. Inicie o Visual Studio Code.
1. Defina a cadeia de conexão do Hub IoT selecionando o ícone **Mais ações** ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo. Copie a cadeia de caracteres do arquivo src/cloud-to-device-console-app/appsettings.json. 

    ![Definir cadeia de conexão do Hub IoT](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Clique com o botão direito do mouse no arquivo src/edge/deployment.template.json e selecione **Gerar o Manifesto de Implantação do IoT Edge**. O Visual Studio Code usa os valores do arquivo .env para substituir as variáveis encontradas no arquivo do modelo de implantação. Essa ação deve criar um arquivo de manifesto na pasta src/edge/config chamado **deployment.amd64.json**.

   ![Gerar um manifesto de implantação do IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Clique com o botão direito do mouse no arquivo src/edge/config/deployment.amd64.json e selecione **Criar Implantação para Dispositivo Único**.

   ![Criar implantação para dispositivo único](./media/quickstarts/create-deployment-single-device.png)
1. Em seguida, você é solicitado a **Selecionar um dispositivo do Hub IoT**. Selecione lva-sample-device na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure no canto inferior esquerdo. Você deverá ver que o dispositivo de borda tem os seguintes módulos implantados:
    * Análise Dinâmica de Vídeo no IoT Edge (nome do módulo **lvaEdge**)
    * Simulador RTSP (nome do módulo **rtspsim**)
 
    ![Hub IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparar para monitorar os módulos 

Quando você usa o módulo da Análise Dinâmica de Vídeo no IoT Edge para gravar a transmissão de vídeo ao vivo, ele envia eventos ao Hub IoT. Para ver esses eventos, siga estas etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure pelo **Hub IoT do Azure** no canto inferior esquerdo.
1. Expanda o nó **Dispositivos**.
1. Clique com o botão direito do mouse no arquivo lva-sample-device e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**.

    ![Iniciar Monitoramento de Ponto de Extremidade de Evento Interno](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="run-the-program"></a>Execute o programa 

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar Mensagem Detalhada":::
1. Vá para src/cloud-to-device-console-app/operations.json.
1. No nó **GraphTopologySet**, edite o seguinte:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Em seguida, sob os nós **GraphInstanceSet** e **GraphTopologyDelete**, verifique se o valor de **topologyName** corresponde ao valor da propriedade **name** na topologia do grafo acima:

    `"topologyName" : "CVRToAMSAsset"`  
1. Abra a [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) em um navegador e examine assetNamePattern. Para verificar se você tem um ativo com um nome exclusivo, convém alterar o nome da instância do grafo no arquivo operations.json (do valor padrão de Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Inicie uma sessão de depuração pressionando F5. Você verá algumas mensagens impressas na janela **TERMINAL**.
1. O arquivo operations.json começa com chamadas para GraphTopologyList e GraphInstanceList. Se você tiver limpado recursos após guias de início rápido ou tutoriais anteriores, essa ação retornará listas vazias e, em seguida, pausará para que você pressione **Enter**, conforme mostrado abaixo:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. Quando você pressiona a tecla **Enter** na janela **TERMINAL**, o próximo conjunto de chamadas de método direto é feito:
   * Uma chamada para GraphTopologySet usando a topologyUrl anterior
   * Uma chamada para GraphInstanceSet usando o corpo a seguir
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
   * Uma chamada para GraphInstanceActivate para iniciar a instância do grafo e iniciar a transmissão de vídeo
   * Uma segunda chamada para GraphInstanceList para mostrar que a instância do grafo está em estado de execução 
1. Agora, a saída na janela **TERMINAL** é pausada com o prompt **Pressione Enter para continuar**. Não pressione **Enter** nesse momento. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Agora, se alternar para a janela **SAÍDA** no Visual Studio Code, você verá as mensagens sendo enviadas para o Hub IoT pela Análise Dinâmica de Vídeo no módulo do IoT Edge.

   Algumas mensagens são discutidas na seção a seguir.
1. A instância do grafo continua sendo executada e continua gravando o vídeo. O simulador RTSP mantém o loop do vídeo de origem. Para interromper a gravação, volte para a janela **TERMINAL** e pressione **Enter**. A próxima série de chamadas é feita para limpar os recursos usando:

   * Uma chamada para GraphInstanceDeactivate para desativar a instância do grafo.
   * Uma chamada para GraphInstanceDelete para excluir a instância.
   * Uma chamada para GraphTopologyDelete para excluir a topologia.
   * Uma chamada final para GraphTopologyList para mostrar que a lista está vazia.

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando você executa o grafo de mídia, o módulo da Análise Dinâmica de Vídeo no IoT Edge envia determinados eventos de diagnóstico e operacionais para o hub do IoT Edge. Esses eventos são as mensagens que você vê na janela **SAÍDA** do Visual Studio Code. Elas contêm uma seção de corpo e uma seção applicationProperties. Para entender o que essas seções representam, confira [Criar e ler as mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo da Análise Dinâmica de Vídeo.

## <a name="diagnostics-events"></a>Eventos de diagnóstico 

### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando a instância do grafo é ativada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no módulo rtspsim. Se for bem-sucedido, ele imprimirá este evento:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* A mensagem é um evento de diagnóstico (MediaSessionEstablished). Ela indica que o nó de origem RTSP (o assunto) estabeleceu uma conexão com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* A seção de assunto em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* A seção eventType em applicationProperties indica que se trata de um evento de diagnóstico.
* A seção eventTime indica a hora em que o evento ocorreu.
* A seção de corpo contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Eventos operacionais 

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando o nó do coletor de ativos começa a gravar vídeo, ele emite esse evento do tipo **Microsoft.Media.Graph.Operational.RecordingStarted**:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

A seção subject em applicationProperties faz referência ao nó do coletor de ativos no grafo, que gerou essa mensagem.

A seção de corpo contém informações sobre o local de saída. Nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado. Anote esse valor.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Como o nome sugere, o evento RecordingStarted é enviado quando a gravação é iniciada, mas os dados de vídeo podem ainda não ter sido carregados para o ativo. Depois de carregar os dados de vídeo no ativo, o nó do coletor de ativos emite este evento do tipo **Microsoft.Media.Graph.Operational.RecordingAvailable**:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

O evento indica que dados suficientes foram gravados no ativo para que os players ou clientes iniciassem a reprodução do vídeo.

A seção subject em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem.

A seção de corpo contém informações sobre o local de saída. Nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Quando você desativa a Instância do Grafo, o nó do coletor de ativos para de gravar vídeo no ativo. Ele emite este evento do tipo **Microsoft.Media.Graph.Operational.RecordingStopped**:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Esse evento indica que a gravação foi interrompida.

A seção subject em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem.

A seção do corpo contém informações sobre o local de saída, que, nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo dos Serviços de Mídia  

Você pode examinar o ativo dos Serviços de Mídia criado pelo grafo de mídia fazendo logon no portal do Azure e exibindo o vídeo.

1. Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.
1. Localize sua conta dos Serviços de Mídia entre os recursos que você tem em sua assinatura e abra o painel da conta.
1. Selecione **Ativos** na lista **Serviços de Mídia**.

    ![Ativos dos Serviços de Mídia](./media/continuous-video-recording-tutorial/assets.png)
1. Você encontrará um ativo listado com o nome sampleAsset-CVRToAMSAsset-Sample-Graph-1. Esse é o padrão de nomenclatura escolhido no arquivo de topologia do grafo.
1. Selecione o ativo.
1. Na página de detalhes do ativo, selecione **Criar** na caixa de texto **URL de Streaming**.

    ![Novo ativo](./media/continuous-video-recording-tutorial/new-asset.png)

1. No assistente que é aberto, aceite as opções padrão e selecione **Adicionar**. Para obter mais informações, confira [Reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Verifique se o [ponto de extremidade de streaming está em execução](../latest/stream-streaming-endpoint-concept.md).
1. O player deve carregar o vídeo. Selecione **Reproduzir** para exibi-lo.

> [!NOTE]
> Como a origem do vídeo era um contêiner simulando um feed de câmera, os carimbos de data/hora do vídeo estão relacionados aos momentos em que você ativou a instância do grafo e a desativou. Para ver como navegar por uma gravação de vários dias e exibir partes do arquivo, confira o tutorial [Reprodução de gravações de vários dias](playback-multi-day-recordings-tutorial.md). Nesse tutorial, você também pode ver os carimbos de data/hora no vídeo exibido na tela.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros tutoriais, mantenha os recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://www.onvif.org/conformant-products/) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, siga as instruções no início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md) para registrar o dispositivo no Hub IoT do Azure.