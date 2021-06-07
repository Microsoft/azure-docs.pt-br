---
title: Tutorial de gravação de vídeo baseada em eventos para a nuvem e reprodução com origem na nuvem – Azure
description: Neste tutorial, você aprenderá a usar a Análise de Vídeo ao vivo do Azure no Azure IoT Edge para registrar uma gravação de vídeo baseada em eventos para a nuvem e a reproduzir com origem na nuvem.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: de1a4d423108ddb18af4f8478c9bac60672d6062
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279809"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Tutorial: Gravação de vídeo baseada em eventos para a nuvem e reprodução com origem na nuvem

Neste tutorial, você aprenderá a usar a Análise de Vídeo ao vivo do Azure no Azure IoT Edge para registrar seletivamente partes de uma fonte de vídeo ao vivo nos Serviços de Mídia do Azure na nuvem. Esse caso de uso é chamado de [EVR](event-based-video-recording-concept.md) (gravação de vídeo baseada em eventos) neste tutorial. Para registrar partes de um vídeo ao vivo, você usará um modelo de IA de detecção de objetos para procurar objetos no vídeo e gravar clipes de vídeo somente quando um determinado tipo de objeto for detectado. Você também aprenderá a reproduzir os clipes de vídeo gravados usando os Serviços de Mídia. Essa funcionalidade é útil para vários cenários em que há a necessidade de manter um arquivo de clipes de vídeo do seu interesse. 

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar os recursos relevantes.
> * Examinar o código que executa a EVR.
> * Executar o código de exemplo.
> * Examinar os resultados e exibir o vídeo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia estes artigos antes de começar:

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md) 
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: Desenvolver um módulo do IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Como editar deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Seção sobre [como declarar rotas no manifesto de implantação do IoT Edge](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos deste tutorial são:

* [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento com as extensões do [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Pode ser solicitado que você instale o Docker. Ignore esse aviso.
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) instalado no computador de desenvolvimento.
* Concluir o [script de instalação de recursos da Análise de Vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [configurar o ambiente](./detect-motion-emit-events-quickstart.md?pivots=programming-language-csharp#set-up-your-development-environment)

No final dessas etapas, você terá os recursos do Azure relevantes implantados em sua assinatura do Azure:

* Hub IoT do Azure
* Conta de Armazenamento do Azure
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](../../iot-edge/how-to-install-iot-edge.md) instalado

> [!TIP]
> Se você tiver problemas com os recursos do Azure que são criados, confira nosso **[guia de solução de problemas](troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas encontrados com frequência.

## <a name="concepts"></a>Conceitos

A gravação de vídeo baseada em eventos se refere ao processo de gravação de vídeo disparado por um evento. Esse evento poderia ser gerado:
- Pelo processamento do próprio sinal do vídeo, por exemplo, ao detectar um objeto se movimentando no vídeo.
- Por uma fonte independente, por exemplo, a abertura de uma porta. 

Opcionalmente, você pode disparar a gravação somente quando um serviço de inferência detectar que ocorreu um evento específico. Neste tutorial, você usará um vídeo de veículos se movendo em uma rodovia e gravará clipes de vídeo sempre que um caminhão for detectado.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Grafo de mídia":::

O diagrama é uma representação em ilustração de um [grafo de mídia](media-graph-concept.md) e módulos adicionais que realizam o cenário desejado. Quatro módulos do IoT Edge são envolvidos:

* Módulo Análise de Vídeo ao vivo no IoT Edge.
* Um módulo de borda executando um modelo de IA por trás de um ponto de extremidade HTTP. Esse módulo de IA usa o modelo [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), que pode detectar muitos tipos de objetos.
* Um módulo personalizado para contar e filtrar objetos, que é conhecido como um Contador de Objetos no diagrama. Você criará um Contador de Objetos e o implantará neste tutorial.
* Um [módulo simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) para simular uma câmera RTSP.
    
Como mostra o diagrama, você usará um nó de [origem RTSP](media-graph-concept.md#rtsp-source) no grafo de mídia para capturar o vídeo ao vivo simulado do tráfego em uma rodovia e enviá-lo para dois caminhos:

* O primeiro caminho é para um nó de extensão HTTP. O nó faz amostragens dos quadros de vídeo em um valor definido por você usando o campo `samplingOptions` e retransmite os quadros, como imagens, para o módulo de IA YOLOv3, que é um detector de objeto. O nó recebe os resultados, que são os objetos (tráfego de veículos) detectados pelo modelo. Em seguida, o nó de extensão HTTP publica os resultados por meio do nó coletor de mensagens do Hub IoT no Hub do IoT Edge.
* O módulo objectCounter é configurado para receber mensagens do Hub do IoT Edge, que incluem os resultados de detecção de objetos (tráfego de veículos). O módulo verifica essas mensagens e procura objetos de um determinado tipo, que foram definidos por meio de uma configuração. Quando tal objeto é encontrado, esse módulo envia uma mensagem ao Hub do IoT Edge. Essas mensagens de "objeto encontrado" são roteadas para o nó de origem do Hub IoT do grafo de mídia. Ao receber tal mensagem, o nó de origem do Hub IoT no grafo de mídia dispara o nó do [processador de portão de sinal](media-graph-concept.md#signal-gate-processor). O nó processador da porta do sinal é aberto por um período de tempo configurado. O vídeo flui pelo portão para o nó do coletor de ativos durante esse período. Essa parte da transmissão ao vivo é registrada por meio do nó do [coletor de ativos](media-graph-concept.md#asset-sink) em um [ativo](terminology.md#asset) da conta dos Serviços de Mídia do Azure.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Antes de começar, verifique se você concluiu o terceiro marcador dos [Pré-requisitos](#prerequisites). Após a conclusão do script de instalação, selecione as chaves para expor a estrutura de pastas. Você verá alguns arquivos criados no diretório ~/clouddrive/lva-sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Configurações do aplicativo":::

Os arquivos de interesse neste tutorial são:

* **~/clouddrive/lva-sample/edge-deployment/.env**: contém propriedades que o Visual Studio Code usa para implantar módulos em um dispositivo de borda.
* **~/clouddrive/lva-sample/appsetting.json**: usado pelo Visual Studio Code para executar o código de exemplo.

Você precisará dos arquivos para estas etapas.

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

## <a name="examine-the-template-file"></a>Examine o arquivo de modelo 

Na etapa anterior, você iniciou o Visual Studio Code e abriu a pasta que contém o código de exemplo.

No Visual Studio Code, navegue até src/edge. Você verá o arquivo .env criado e alguns arquivos do modelo de implantação. Este modelo define quais módulos de borda serão implantados no dispositivo de borda (a VM Linux do Azure). O arquivo .env contém valores para as variáveis usadas nesses modelos, como as credenciais dos Serviços de Mídia.

Abra src/edge/deployment.objectCounter.template.json. Há quatro entradas na seção **módulos** que correspondem aos itens listados na seção “Conceitos”:

* **lvaEdge**: é a Análise Dinâmica de Vídeo no módulo do IoT Edge.
* **yolov3**: é o módulo de IA criado usando o modelo YOLO v3.
* **rtspsim**: é o simulador RTSP.
* **objectCounter**: é o módulo que procura objetos específicos nos resultados do yolov3.

Para o módulo objectCounter, confira a cadeia de caracteres (${MODULEs.objectCounter}) usada para o valor "image". Isso se baseia no [tutorial](../../iot-edge/tutorial-develop-for-linux.md) sobre como desenvolver um módulo do IoT Edge. O Visual Studio Code reconhece automaticamente que o código para o módulo objectCounter está em src/edge/modules/objectCounter. 

Leia [esta seção](../../iot-edge/module-composition.md#declare-routes) sobre como declarar rotas no manifesto de implantação do IoT Edge. Em seguida, examine as rotas no arquivo JSON do modelo. Observe como:

* O LVAToObjectCounter é usado para enviar eventos específicos para um ponto de extremidade específico no módulo objectCounter.
* O ObjectCounterToLVA é usado para enviar um evento de gatilho para um ponto de extremidade específico (que deve ser o nó de origem do Hub IoT) no módulo lvaEdge.
* O objectCounterToIoTHub é usado como ferramenta de depuração, para ajudar a enxergar a saída do objectCounter ao executar este tutorial.

> [!NOTE]
> Verifique as propriedades desejadas do módulo objectCounter, que são configuradas para procurar por objetos marcados como "caminhão", com um nível de confiança de pelo menos 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge 

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e as definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.

Usando o Visual Studio Code, siga [estas instruções](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) para entrar no Docker. Em seguida, selecione **Compilar e efetuar push da solução do IoT Edge**. Use src/edge/deployment.objectCounter.template.json para esta etapa.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Compilar a solução do IoT Edge e efetuar push dela":::

Essa ação cria o módulo objectCounter para contagem de objetos e vai efetuar push da imagem para o Registro de Contêiner do Azure.

* Verifique se há as variáveis de ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no arquivo .env.

A etapa cria o manifesto de implantação do IoT Edge em src/edge/config/deployment.objectCounter.amd64.json. Clique com o botão direito do mouse nesse arquivo e selecione **Criar implantação para dispositivo único**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Criar implantação para dispositivo único":::

Se este for seu primeiro tutorial com a Análise de Vídeo ao vivo no IoT Edge, o Visual Studio Code solicitará que você insira a cadeia de conexão do Hub IoT. Você pode copiá-la do arquivo appsettings.json.

> [!NOTE]
> Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Em seguida, o Visual Studio Code solicitará que você selecione um dispositivo do Hub IoT. Selecione seu dispositivo do IoT Edge, que deve ser lva-sample-device.

Nesta fase, a implantação dos módulos de borda em seu dispositivo do IoT Edge já foi iniciada.
Em cerca de 30 segundos, atualize o Hub IoT do Azure no canto inferior esquerdo no Visual Studio Code. Você verá que há quatro módulos implantados chamados lvaEdge, rtspsim, yolov3 e objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Quatro módulos implantados":::

## <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Para ver os eventos do módulo objectCounter e do módulo de Análise de Vídeo ao vivo no IoT Edge, execute as seguintes etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure pelo **Hub IoT do Azure** no canto inferior esquerdo.
1. Expanda o nó **Dispositivos**.
1. Clique com o botão direito do mouse no arquivo lva-sample-device e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Iniciar monitoramento do ponto de extremidade de evento interno":::

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
1. <!--In Visual Studio Code, go-->Vá para src/cloud-to-device-console-app/operations.json.
1. No nó **GraphTopologySet**, edite o seguinte:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. Em seguida, nos nós **GraphInstanceSet** e **GraphTopologyDelete**, edite:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
            "topologyName": "EVRtoAssetsOnObjDetect",
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
    
   * Uma chamada para GraphInstanceActivate para iniciar a instância do grafo e iniciar a transmissão do vídeo
   * Uma segunda chamada para GraphInstanceList para mostrar que a instância do grafo está em estado de execução
     
1. Agora, a saída na janela **TERMINAL** é pausada com o prompt **Pressione Enter para continuar**. Não pressione **Enter** nesse momento. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Agora, se alternar para a janela **SAÍDA** no Visual Studio Code, você verá as mensagens sendo enviadas para o Hub IoT pela Análise Dinâmica de Vídeo no módulo do IoT Edge.

   Algumas mensagens são discutidas na seção a seguir.
1. A instância do grafo continua sendo executada e continua gravando o vídeo. O simulador RTSP mantém o loop do vídeo de origem. Examine as mensagens conforme discutido na seção a seguir. Para interromper a instância, volte para a janela **TERMINAL** e selecione **Enter**. A próxima série de chamadas é feita para limpar os recursos usando:

   * Uma chamada para GraphInstanceDeactivate para desativar a instância do grafo.
   * Uma chamada para GraphInstanceDelete para excluir a instância.
   * Uma chamada para GraphTopologyDelete para excluir a topologia.
   * Uma chamada final para GraphTopologyList para mostrar que a lista está vazia.

## <a name="interpret-the-results"></a>Interpretar os resultados 

Quando você executa o grafo de mídia, o módulo da Análise Dinâmica de Vídeo no IoT Edge envia determinados eventos de diagnóstico e operacionais para o hub do IoT Edge. Esses eventos são as mensagens que você vê na janela **SAÍDA** do Visual Studio Code. Elas contêm uma seção de corpo e uma seção applicationProperties. Para entender o que essas seções representam, confira [Criar e ler as mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, as propriedades do aplicativo e o conteúdo do corpo são definidos pelo módulo da Análise Dinâmica de Vídeo.

## <a name="diagnostics-events"></a>Eventos de diagnóstico

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished 

Quando um grafo de mídia é instanciado, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner do simulador RTSP. Se for bem-sucedido, ele imprimirá este evento. O tipo de evento é **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* A mensagem é um evento de diagnóstico (MediaSessionEstablished). Ela indica que o nó de origem RTSP (o assunto) estabeleceu uma conexão com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* A seção de assunto em applicationProperties faz referência ao nó na topologia do grafo da qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* A seção eventType em applicationProperties indica que se trata de um evento de diagnóstico.
* A seção eventTime indica a hora em que o evento ocorreu. Essa é a hora em que o vídeo do tráfego (arquivo MKV) começou a chegar ao módulo como uma transmissão ao vivo.
* A seção de corpo contém dados sobre o evento de diagnóstico, que, nesse caso, são os detalhes de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Eventos operacionais

Depois que o grafo de mídia for executado por um tempo, cedo ou tarde você receberá um evento do módulo objectCounter. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

A seção applicationProperties contém a hora do evento. Essa é a hora em que o módulo objectCounter observou que os resultados do módulo yolov3 continham objetos de interesse (caminhões).

Você poderá ver mais desses eventos surgirem à medida que outros caminhões forem detectados no vídeo.

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quase imediatamente após o Contador de Objetos enviar o evento, você verá um evento do tipo **Microsoft.Media.Graph.Operational.RecordingStarted**:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

A seção subject em applicationProperties faz referência ao nó do coletor de ativos no grafo, que gerou essa mensagem. A seção de corpo contém informações sobre o local de saída. Nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado. Anote esse valor.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Quando o nó do coletor de ativos tiver carregado o vídeo no ativo, ele emitirá este evento do tipo **Microsoft.Media.Graph.Operational.RecordingAvailable**:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

O evento indica que dados suficientes foram gravados no ativo para que os players ou clientes iniciassem a reprodução do vídeo. A seção subject em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem. A seção de corpo contém informações sobre o local de saída. Nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Se você examinar as configurações de ativação (maximumActivationTime) para o nó do processador do portão do sinal na [topologia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), verá que o portão está configurado para fechar 30 segundos após o envio do vídeo. Aproximadamente 30 segundos após o evento RecordingStarted, você deverá ver um evento do tipo **Microsoft.Media.Graph.Operational.RecordingStopped**. Esse evento indica que o nó do coletor de ativos parou de gravar vídeo no ativo.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Esse evento indica que a gravação foi interrompida. A seção subject em applicationProperties faz referência ao nó AssetSink no grafo, o qual gerou essa mensagem. A seção de corpo contém informações sobre o local de saída. Nesse caso, é o nome do ativo dos Serviços de Mídia do Azure no qual o vídeo é gravado.

## <a name="media-services-asset"></a>Ativo dos Serviços de Mídia  

Você vai poder examinar o ativo de Serviços de Mídia criado pelo grafo de mídia fazendo logon no portal do Azure e exibindo o vídeo.

1. Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.
1. Localize sua conta dos Serviços de Mídia entre os recursos que você tem em sua assinatura. Abra o painel da conta.
1. Selecione **Ativos** na lista **Serviços de Mídia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Gravação de vídeo contínua":::
1. Você encontrará um ativo listado com o nome sampleAssetFromEVR-LVAEdge-{DateTime}. Esse é o nome fornecido na propriedade outputLocation do evento RecordingStarted. O assetNamePattern na topologia determina como esse nome foi gerado.
1. Selecione o ativo.
1. Na página de detalhes do ativo, selecione **Criar** na caixa de texto **URL de Streaming**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Novo ativo":::
1. No assistente que é aberto, aceite as opções padrão e selecione **Adicionar**. Para obter mais informações, confira [reprodução de vídeo](video-playback-concept.md).

    > [!TIP]
    > Verifique se o [ponto de extremidade de streaming está em execução](../latest/stream-streaming-endpoint-concept.md).
1. O player deve carregar o vídeo. Selecione **Reproduzir** para exibi-lo.

> [!NOTE]
> Como a origem do vídeo era um contêiner simulando um feed de câmera, os carimbos de data/hora do vídeo estão relacionados aos momentos em que você ativou a instância do grafo e a desativou. Se você usar os controles de reprodução incorporados ao tutorial [Reprodução de gravações de vários dias](playback-multi-day-recordings-tutorial.md), você poderá ver os carimbos de data/hora do vídeo exibidos na tela.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros tutoriais, mantenha os recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Você pode pesquisar por câmeras IP compatíveis com RTSP na [página de produtos compatíveis com ONVIF](https://www.onvif.org/conformant-products/) ao procurar por dispositivos que estejam de acordo com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 (em vez de usar uma VM Linux do Azure). Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, siga as instruções no início rápido [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md) para registrar o dispositivo no Hub IoT do Azure.