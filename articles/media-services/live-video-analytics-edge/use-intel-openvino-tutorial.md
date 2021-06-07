---
title: Analisar vídeos ao vivo usando o OpenVINO™ Model Server – Extensão de IA da Intel
description: Neste tutorial, você usará um servidor de modelo de IA fornecido pela Intel para analisar o feed de vídeos ao vivo de uma câmera de IP (simulada).
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 5751184493fffeeaf647507e9e9b00834f63ab5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557251"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Tutorial: Analisar vídeos ao vivo usando o OpenVINO™ Model Server – Extensão de IA da Intel 

Este tutorial mostra como usar o OpenVINO™ Model Server – Extensão de IA da Intel para analisar um feed de vídeos ao vivo de uma câmera de IP (simulada). Você verá como esse servidor de inferência fornece acesso a modelos para detectar objetos (uma pessoa, um veículo ou uma bicicleta) e um modelo para classificar veículos. Um subconjunto dos quadros no feed de vídeos ao vivo é enviado para um servidor de inferência e os resultados correspondentes são enviados para o Hub do IoT Edge.

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

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Neste guia de início rápido, você usará a Análise Dinâmica de Vídeo no IoT Edge junto com o OpenVINO™ Model Server – Extensão de IA da Intel para detectar objetos como veículos ou classificá-los. Você publicará os eventos de inferência resultantes no Hub do IoT Edge.

## <a name="overview"></a>Visão geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Visão geral":::

O diagrama mostra como os sinais fluem neste guia de início rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de extensão HTTP](media-graph-concept.md#http-extension-processor). 

O nó de extensão HTTP desempenha a função de um proxy. Ele amostra os quadros de vídeo de entrada definidos por você, campo `samplingOptions`, e também converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem por REST para outro módulo do Edge que executa modelos de IA por trás de um ponto de extremidade HTTP. Neste exemplo, esse módulo do Edge é o OpenVINO™ Model Server – Extensão de IA da Intel. O nó de processador de extensão HTTP reúne os resultados da detecção e publica eventos no nó do [coletor do Hub IoT](media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [Hub do IoT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Neste tutorial, você irá:

1. Criar e implantar o grafo de mídia, modificando-o.
1. Interpretar os resultados.
1. Limpar os recursos.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Sobre o OpenVINO™ Model Server – Extensão de IA da Intel

A Distribuição Intel® do [Kit de ferramentas OpenVINO™](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (inferência visual aberta e otimização de rede neural) é um kit de software gratuito que ajuda os desenvolvedores e cientistas de dados a acelerar as cargas de trabalho da pesquisa visual computacional, simplificar a inferência e as implantações de aprendizado profundo e permitir a execução heterogênea e fácil entre plataformas Intel® da borda para nuvem. Ela inclui o Kit de Ferramentas de Implantação de Aprendizado Profundo da Intel® com o otimizador de modelo e o mecanismo de inferência e o repositório [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo), que inclui mais de 40 modelos pré-treinados otimizados.

Para criar soluções de análise dinâmica de vídeo complexas de alto desempenho, a Análise Dinâmica de Vídeo no módulo do IoT Edge deve ser emparelhada com um poderoso mecanismo de inferência que pode aproveitar a escala na borda. Neste tutorial, as solicitações de inferência são enviadas para o [OpenVINO™ Model Server – Extensão de IA da Intel](https://aka.ms/lva-intel-ovms), um módulo do Edge que foi projetado para trabalhar com a Análise Dinâmica de Vídeo no IoT Edge. Esse módulo de servidor de inferência contém o OVMS (OpenVINO™ Model Server), um servidor de inferência da plataforma do kit de ferramentas OpenVINO™, que é altamente otimizado para cargas de trabalho da Pesquisa Visual Computacional e é desenvolvido para arquiteturas Intel®. Uma extensão foi adicionada ao OVMS para fácil troca de quadros de vídeo e resultados de inferência entre o servidor de inferência e a Análise Dinâmica de Vídeo no módulo do IoT Edge, permitindo que você execute qualquer modelo com suporte do kit de ferramentas do OpenVINO (você pode personalizar o módulo de servidor de inferência modificando o [código](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)). Você pode escolher ainda mais entre a ampla variedade de mecanismos de aceleração fornecidos pelo hardware Intel®. Eles incluem CPUs (Atom, Core, Xeon), FPGAs e VPUs.

Na versão inicial desse servidor de inferência, você tem acesso aos seguintes [modelos](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Detecção de veículo (URL de inferência: http://{nome-do-módulo}:4000/vehicleDetection)
- Detecção de pessoa/veículo/bicicleta (URL de inferência: http://{nome-do-módulo}:4000/personVehicleBikeDetection)
- Classificação de veículo (URL de inferência: http://{nome-do-módulo}:4000/vehicleClassification)
- Detecção facial (URL de inferência: http://{nome-do-módulo}:4000/faceDetection)

> [!NOTE]
> Ao baixar e usar o módulo do Edge: OpenVINO™ Model Server – Extensão de IA da Intel e o software incluído, você concorda com os termos e condições no [Contrato de Licença](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> A Intel está comprometida a respeitar os direitos humanos e evitar cumplicidade em abusos de direitos humanos. Confira os [Princípios de direitos humanos globais da Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Os produtos e o software da Intel se destinam a serem usados somente em aplicativos que não causam ou contribuem com a violação de direitos humanos reconhecidos internacionalmente.

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * Em `GraphInstanceSet`, edite o nome da topologia de grafo para corresponder ao valor no link anterior:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Em `GraphTopologyDelete`, edite o nome:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

1. Clique com o botão direito do mouse no arquivo *src/edge/deployment.openvino.template.json* e selecione **Gerar o Manifesto de Implantação do IoT Edge**.

    ![Gerar um Manifesto de Implantação do IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    O arquivo de manifesto *deployment.openvino.amd64.json* é criado na pasta *src/edge/config*.

1. Se você concluiu o início rápido [Detectar movimento e emitir eventos](detect-motion-emit-events-quickstart.md), ignore esta etapa. 

    Caso contrário, ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **Definir Cadeia de Conexão do Hub IoT**. Você pode copiar a cadeia de caracteres do arquivo *appsettings.json*. Ou, para garantir que configurou o Hub IoT adequado dentro do Visual Studio Code, use o [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Definir cadeia de conexão do Hub IoT](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Clique com o botão direito do mouse em *src/edge/config/deployment.openvino.amd64.json* e selecione **Criar Implantação para Dispositivo Único**. 

    ![Criar implantação para dispositivo único](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando receber um prompt para selecionar um dispositivo do Hub IoT, selecione **lva-sample-device**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:

    * O módulo da Análise Dinâmica de Vídeo, chamado **lvaEdge**
    * Um módulo chamado **rtspsim**, que simula um servidor RTSP e atua como a origem de um feed de vídeo ao vivo
    * O módulo **openvino**, que é o módulo do OpenVINO™ Model Server – Extensão de IA da Intel 

### <a name="prepare-to-monitor-events"></a>Preparar-se para monitorar eventos

Clique com o botão direito do mouse no dispositivo de Análise Dinâmica de Vídeo e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela **SAÍDA** do Visual Studio Code. 

![Começar a monitorar](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Executar o programa de exemplo para detectar veículos
Se você abrir a [topologia de grafo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) deste tutorial em um navegador, verá que o valor de `inferencingUrl` foi definido como `http://openvino:4000/vehicleDetection`, o que significa que o servidor de inferência retornará resultados após a detecção de veículos, se houver, no vídeo ao vivo.

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
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
             "topologyName": "InferencingWithOpenVINO",
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
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

Nesta mensagem, observe estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o assunto) se conectou com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties`, `subject` indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O `eventTime` indica a hora em que o evento ocorreu.
* O `body` contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do módulo do OpenVINO™ Model Server – Extensão de IA. Em seguida, ele emite os resultados por meio do nó do coletor do Hub IoT como eventos de inferência. 

Nesses eventos, o tipo é definido como `entity` para indicar que se trata de uma entidade, como um carro ou um caminhão. O valor de `eventTime` é a hora, em UTC, em que o objeto foi detectado. 

No exemplo a seguir, dois veículos foram detectados com valores de confiança acima de 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:43:18.1280000Z"
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
Para usar um modelo diferente, você precisará modificar a topologia do grafo, bem como o arquivo `operations.json`.

Copie a [topologia de grafo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) para um arquivo local, digamos `C:\TEMP\topology.json`. Abra essa cópia e edite o valor de `inferencingUrl` para `http://openvino:4000/personVehicleBikeDetection`.

Em seguida, no Visual Studio Code, acesse a pasta *src/cloud-to-device-console-app* e abra o arquivo `operations.json`. Edite a linha com `topologyUrl` para:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Agora, você pode repetir as etapas acima para executar o programa de exemplo novamente, com a nova topologia. Os resultados da inferência serão semelhantes (no esquema) aos do modelo de detecção de veículo, com apenas o `subtype` definido como `personVehicleBikeDetection`.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Executar o programa de exemplo para classificar veículos
No Visual Studio Code, abra a cópia local de `topology.json` da etapa anterior e edite o valor de `inferencingUrl` para `http://openvino:4000/vehicleClassification`. Se você tiver executado o exemplo anterior para detectar pessoas, veículos ou bicicletas, não será necessário modificar o arquivo `operations.json` novamente.

Agora, você pode repetir as etapas acima para executar o programa de exemplo novamente, com a nova topologia. Veja abaixo um resultado de classificação de exemplo.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Executar o programa de exemplo para detectar rostos
No Visual Studio Code, abra a cópia local de `topology.json` da etapa anterior e edite o valor de `inferencingUrl` para `http://openvino:4000/faceDetection`. Se você tiver executado o exemplo anterior para detectar pessoas, veículos ou bicicletas, não será necessário modificar o arquivo `operations.json` novamente.

Agora, você pode repetir as etapas acima para executar o programa de exemplo novamente, com a nova topologia. Veja abaixo um resultado de detecção de exemplo. (Observação: o vídeo do estacionamento usado acima não contém nenhuma face detectável; você deve usar outro vídeo para experimentar esse modelo).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime&quot;: &quot;2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros guias de início rápido ou tutoriais, mantenha os recursos criados. Caso contrário, acesse o portal do Azure, acesse os seus grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Pesquise pelas câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 em vez de usar uma VM Linux do Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, registre o dispositivo no Hub IoT do Azure seguindo as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md).