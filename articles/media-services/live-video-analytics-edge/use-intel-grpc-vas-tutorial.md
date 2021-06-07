---
title: Analisar vídeo ao vivo usando o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel via gRPC
description: Este tutorial mostra como usar o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel para analisar um feed de vídeos ao vivo de uma câmera de IP (simulada).
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 07a7daf6363f0e528f84635ed6713ac462f89ca5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562844"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutorial: Analisar vídeo ao vivo usando o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel 

Este tutorial mostra como usar o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel para analisar um feed de vídeos ao vivo de uma câmera de IP (simulada). Você verá como esse servidor de inferência fornece acesso a diferentes modelos para detecção de objetos (uma pessoa, um veículo ou uma bicicleta) e classificação de objetos (atributos do veículo), bem como um modelo para acompanhamento de objetos (pessoa, veículo e bicicleta). A integração com o módulo gRPC permite que você envie quadros de vídeo para o servidor de inferência de IA. Os resultados, por sua vez, são enviados ao Hub do IoT Edge. Ao executar esse serviço de inferência no mesmo nó de computação que a Análise Dinâmica de Vídeo, você pode aproveitar o envio de dados de vídeo por meio da memória compartilhada. Assim, você pode executar a inferência na taxa de quadros do feed de vídeo ao vivo (por exemplo, 30 quadros/s). 

Este tutorial usa uma VM do Azure como dispositivo do IoT Edge, bem como um fluxo de vídeo ao vivo simulado. Ele é baseado em um código de exemplo escrito em C# e no início rápido [Detectar movimento e emitir eventos](detect-motion-emit-events-quickstart.md).

> [!NOTE]
> Este tutorial requer o uso de um computador x86-64 como seu dispositivo Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure que inclua uma assinatura ativa. [Crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se não tiver uma.
  > [!NOTE]
  > Você precisará ter uma assinatura do Azure com permissões para criar entidades de serviço (a **função de proprietário** fornece isso). Caso não tenha as permissões corretas, entre em contato com o administrador da conta para conceder a você as permissões corretas. 
* [Visual Studio Code](https://code.visualstudio.com/) com as extensões a seguir:
    * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se não tiver concluído o guia de início rápido [Detectar eventos de movimento e emissão](detect-motion-emit-events-quickstart.md), não deixe de concluir as etapas para [configurar os recursos do Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Ao instalar o Azure IoT Tools, talvez você receba um prompt para instalar o Docker. Você pode ignorar o prompt.

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

Quando você configura os recursos do Azure, um vídeo curto de um estacionamento é copiado para a VM do Linux no Azure que você está usando como o dispositivo do IoT Edge. Este guia de início rápido usa o arquivo de vídeo para simular uma transmissão ao vivo.

Abra um aplicativo como o [VLC Media Player](https://www.videolan.org/vlc/). Selecione CTRL + N e cole um link para [o vídeo](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para iniciar a reprodução. Você vê a filmagem de veículos em um estacionamento, a maioria deles estacionado e um em movimento.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Neste início rápido, você usará a Análise Dinâmica de Vídeo no IoT Edge, em conjunto com o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel, para detectar objetos, como veículos, e classificá-los, além de acompanhar veículos, pessoas ou bicicletas. Você publicará os eventos de inferência resultantes no Hub do IoT Edge.

## <a name="overview"></a>Visão geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Visão geral do LVA MediaGraph":::

O diagrama mostra como os sinais fluem neste guia de início rápido. Um [módulo do Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de extensão gRPC](media-graph-concept.md#grpc-extension-processor). 

O nó do processador de extensão gRPC usa quadros de vídeo decodificados como entrada e transmite esses quadros para um ponto de extremidade [gRPC](terminology.md#grpc) exposto por um servidor gRPC. O nó dá suporte à transferência de dados usando [memória compartilhada](https://en.wikipedia.org/wiki/Shared_memory) ou inserindo diretamente o conteúdo no corpo de mensagens gRPC. Além disso, o nó tem um formatador de imagem interno para dimensionar e codificar os quadros de vídeo antes que eles sejam retransmitidos para o ponto de extremidade gRPC. O escalonador tem opções para que a taxa de proporção da imagem seja preservada, preenchida ou alongada. O codificador de imagem dá suporte aos formatos JPEG, png e bmp. Saiba mais sobre o processador [aqui](media-graph-extension-concept.md#grpc-extension-processor).

Neste tutorial, você irá:

1. Implantar o grafo de mídia.
1. Interpretar os resultados.
1. Limpe os recursos.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Sobre o Módulo OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel


O módulo OpenVINO™ DL Streamer – Extensão de IA do Edge é um microsserviço baseado no Fornecimento de VA (Fornecimento de Análise de Vídeo) da Intel que fornece pipelines de análise de vídeo criados com o OpenVINO™ DL Streamer. Os desenvolvedores podem enviar quadros de vídeo decodificados para o módulo de extensão de IA que executa a detecção, a classificação ou o acompanhamento e retorna os resultados. O módulo de extensão de IA expõe APIs gRPC compatíveis com plataformas de análise de vídeo, como a Análise Dinâmica de Vídeo no IoT Edge da Microsoft. 

Para criar soluções de análise dinâmica de vídeo complexas de alto desempenho, a Análise Dinâmica de Vídeo no módulo do IoT Edge deve ser emparelhada com um poderoso mecanismo de inferência que pode aproveitar a escala na borda. Neste tutorial, solicitações de inferência são enviadas ao [OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel](), um módulo do Edge projetado para trabalhar com a Análise Dinâmica de Vídeo no IoT Edge. 

Na versão inicial desse servidor de inferência, você tem acesso aos seguintes [modelos](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options):

- object_detection para person_vehicle_bike_detection ![detecção de objetos para veículos](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification para vehicle_attributes_recognition ![classificação de objetos para veículos](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking para person_vehicle_bike_tracking ![acompanhamento de objetos para pessoas e veículos](./media/use-intel-openvino-tutorial/object-tracking.png)

Ele usa os pipelines de Detecção de objetos pré-carregados, Classificação de objetos e Acompanhamento de objetos para começar rapidamente. Além disso, vem com os modelos [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) e [vehicle-attributes-recognition-barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md) pré-carregados.

> [!NOTE]
> Ao baixar e usar o módulo do Edge: OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel, bem como o software incluído, você concorda com os termos e condições definidos no [Contrato de Licença](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> A Intel está comprometida a respeitar os direitos humanos e evitar cumplicidade em abusos de direitos humanos. Confira os [Princípios de direitos humanos globais da Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Os produtos e o software da Intel se destinam a serem usados somente em aplicativos que não causam ou contribuem com a violação de direitos humanos reconhecidos internacionalmente.

Você pode usar a flexibilidade dos diferentes pipelines para seu caso de uso específico alterando apenas as variáveis de ambiente do pipeline em seu modelo de implantação. Isso permite que você altere rapidamente o modelo do pipeline e, quando combinado com a Análise Dinâmica de Vídeo, é uma questão de segundos para alterar o pipeline de mídia e o modelo de inferência.  

## <a name="create-and-deploy-the-media-graph"></a>Criar e implantar o grafo de mídia

### <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo

Como parte dos pré-requisitos, você baixou o código de exemplo para uma pasta. Siga estas etapas para examinar e editar os arquivos de exemplo.

1. No Visual Studio Code, vá até *src/edge*. Você verá seu arquivo *.env* e alguns arquivos do modelo de implantação.

    O modelo de implantação se refere ao manifesto de implantação do dispositivo de borda. Ele inclui alguns valores de espaço reservado. O arquivo *.env* inclui os valores dessas variáveis.

1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:

    * ***c2d-console-app.csproj*** – o arquivo de projeto para o Visual Studio Code.
    * ***operations.json*** – uma lista das operações que você deseja que o programa execute.
    * ***Program.cs*** – o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](direct-methods.md).
        * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos que foram gerados pelo módulo na janela **SAÍDA**.
        * Invoca métodos diretos para limpar recursos.


1. Edite o arquivo *operations.json*:
    * Altere o link para a topologia do grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Em `GraphInstanceSet`, edite o nome da topologia de grafo para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Em `GraphTopologyDelete`, edite o nome:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

1. Clique com o botão direito do mouse no arquivo *src/edge/deployment.openvino.grpc.cpu.template.json* e selecione **Gerar o Manifesto de Implantação do IoT Edge**.

    ![Gerar um Manifesto de Implantação do IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    O arquivo de manifesto *deployment.openvino.grpc.cpu.amd64.json* é criado na pasta *src/edge/config*.

> [!NOTE]
> Também incluímos um modelo *deployment.openvino.grpc.gpu.template.json* que habilita o suporte de GPU para o módulo OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel. Esses modelos apontam para a imagem do Hub do Docker da Intel.

Os modelos mencionados acima apontam para a imagem do Hub do Docker da Intel. Se quiser hospedar uma cópia em seu Registro de Contêiner do Azure, siga as etapas 1 e 2 abaixo:
1. Conecte-se via SSH a um dispositivo com as ferramentas da CLI do Docker instaladas (isto é, o dispositivo de borda) e efetue pull/marque/efetue push do contêiner seguindo estas etapas:
    * Efetue pull da imagem da Intel do Hub do Docker:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Marque a imagem da Intel com seu nome de Registro de Contêiner do Azure. Substitua {YOUR ACR NAME} pelo nome do ACR que você pode encontrar no arquivo .env:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Efetue push da imagem marcada para o Registro de Contêiner do Azure:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Agora, você precisa editar os modelos para fazer referência à nova imagem hospedada no Registro de Contêiner do Azure.
    * Clique com o botão direito do mouse em *deployment.openvino.grpc.cpu.template.json*, navegue até a parte do módulo *lavExtension* e substitua:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        por:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Repita a etapa 2 para *deployment.openvino.grpc.gpu.template.json*


3. Se você concluiu o início rápido [Detectar movimento e emitir eventos](detect-motion-emit-events-quickstart.md), ignore esta etapa. 

    Caso contrário, ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **Definir Cadeia de Conexão do Hub IoT**. Você pode copiar a cadeia de caracteres do arquivo *appsettings.json*. Ou, para garantir que configurou o Hub IoT adequado dentro do Visual Studio Code, use o [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do Hub IoT](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Clique com o botão direito do mouse em *src/edge/config/deployment.openvino.grpc.cpu.template.json* e selecione **Criar Implantação para Dispositivo Único**. 

    ![Criar implantação para dispositivo único](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando receber um prompt para selecionar um dispositivo do Hub IoT, selecione **lva-sample-device**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:

    * O módulo da Análise Dinâmica de Vídeo, chamado **lvaEdge**
    * Um módulo chamado **rtspsim**, que simula um servidor RTSP e atua como a origem de um feed de vídeo ao vivo
    * O módulo **lvaExtension**, que é o OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel 

### <a name="prepare-to-monitor-events"></a>Preparar-se para monitorar eventos

Clique com o botão direito do mouse no dispositivo de Análise Dinâmica de Vídeo e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela **SAÍDA** do Visual Studio Code. 

![Começar a monitorar](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Executar o programa de exemplo para detectar veículos, pessoas ou bicicletas
Se abrir a [topologia do grafo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) deste tutorial em um navegador, você verá que o valor de `grpcExtensionAddress` foi definido como `tcp://lvaExtension:5001`, em comparação com o exemplo *httpExtensionOpenVINO*. Você não precisará alterar a URL para o servidor gRPC. Em vez disso, você instrui o módulo a executar um pipeline específico pelas variáveis de ambiente, conforme mencionado anteriormente. No modelo padrão, definimos isto como: "object_detection" para "person_vehicle_bike_detection". Você pode experimentar outros pipelines compatíveis. 

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione CTRL + SHIFT + X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar Mensagem Detalhada":::
1. Para iniciar uma sessão de depuração, pressione F5. Você verá mensagens impressas na janela **TERMINAL**.
1. O código *operations.json* começa fazendo chamadas para os métodos diretos `GraphTopologyList` e `GraphInstanceList`. Se você limpou os recursos após a conclusão dos inícios rápidos anteriores, esse processo retornará listas vazias e, em seguida, pausará. Para continuar, pressione Enter.

    A janela **TERMINAL** mostra o próximo conjunto de chamadas de método direto:

     * Uma chamada para `GraphTopologySet` que usa o `topologyUrl` anterior
     * Uma chamada para `GraphInstanceSet` que usa o seguinte corpo:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * Uma chamada para `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo
     * Uma segunda chamada para `GraphInstanceList` que mostra que a instância do grafo está em estado de execução
1. A saída na janela **TERMINAL** pausa em um prompt `Press Enter to continue`. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter. 

    A próxima série de chamadas limpa os recursos:
      * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
      * Uma chamada para `GraphInstanceDelete` exclui a instância.
      * Uma chamada para `GraphTopologyDelete` exclui a topologia.
      * Uma chamada final para `GraphTopologyList` mostra que a lista está vazia.

## <a name="interpret-results"></a>Interpretar os resultados

Quando você executa o grafo de mídia, os resultados do nó do processador de extensão HTTP passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela **SAÍDA** contêm uma seção `body` e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo. 

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso. O tipo de evento é **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Nesta mensagem, observe estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o assunto) se conectou com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties`, `subject` indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O `eventTime` indica a hora em que o evento ocorreu.
* O `body` contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó do processador da extensão gRPC recebe resultados de inferência do OpenVINO™ DL Streamer – Extensão de IA do Edge da Intel. Em seguida, ele emite os resultados por meio do nó do coletor do Hub IoT como eventos de inferência. 

Nesses eventos, o tipo é definido como `entity` para indicar que se trata de uma entidade, como um carro ou um caminhão. O valor de `eventTime` é a hora, em UTC, em que o objeto foi detectado. 

No exemplo a seguir, você vê que ele identificou um veículo, o tipo do veículo (van) e sua cor (branco), tudo com um nível de confiança acima de 0,9. Ele também atribuiu uma ID à entidade quando usamos o modelo de acompanhamento de objetos.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id&quot;: &quot;1"
      }
    }
}
```

Nas mensagens, observe os seguintes detalhes:

* Em `applicationProperties`, `subject` faz referência ao nó na topologia do grafo do qual a mensagem foi gerada. 
* Em `applicationProperties`, `eventType` indica que este é um evento de análise.
* O valor `eventTime` é a hora em que o evento ocorreu.
* A seção `body` contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém dados de `inferences`.
* A seção `inferences` indica que o `type` é `entity`. Esta seção inclui dados adicionais sobre a entidade.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Executar o programa de exemplo para detectar pessoas, veículos ou bicicletas
Para usar um modelo diferente, você precisará alterar o modelo de implantação. Para alternar entre os modelos com suporte, você pode alterar as variáveis de ambiente localizadas no módulo lvaExtenstion. Confira este [documento no GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) para obter os valores e as combinações com suporte para os modelos.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Copie o armazene o modelo com um novo nome para cada pipeline possível. Dessa forma, você pode alternar entre modelos criando uma implantação com base em um desses modelos.

Depois de alterar as variáveis, você pode implantar o modelo novamente no dispositivo. Agora, você pode repetir as etapas acima para executar o programa de exemplo novamente, com o novo pipeline. Os resultados da inferência serão semelhantes (no esquema), mas mostrarão mais ou menos informações dependendo do modelo de pipeline escolhido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros guias de início rápido ou tutoriais, mantenha os recursos criados. Caso contrário, acesse o portal do Azure, acesse os seus grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Pesquise pelas câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.
* Use um dispositivo Linux x64 Intel em vez de uma VM Linux do Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, registre o dispositivo no Hub IoT do Azure seguindo as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md).