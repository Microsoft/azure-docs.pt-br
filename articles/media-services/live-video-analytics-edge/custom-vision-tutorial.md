---
title: Analisar vídeos ao vivo com Análise Dinâmica de Vídeo no IoT Edge e na Visão Personalizada do Azure
description: Saiba como usar a Visão Personalizada do Azure para criar um modelo em contêineres que pode detectar um caminhão de brinquedo e usar a funcionalidade de extensibilidade de IA da Análise Dinâmica de Vídeo do Azure no Azure IoT Edge para implantar o modelo na borda e detectar caminhões de brinquedo em um fluxo de vídeo ao vivo.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1abf123883a89bb41909e8aa67aedfadffc3d37e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561195"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analisar vídeos ao vivo com Análise Dinâmica de Vídeo no IoT Edge e na Visão Personalizada do Azure

Neste tutorial, você aprenderá a usar a [Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) do Azure para criar um modelo em contêineres que pode detectar um caminhão de brinquedo e usar a [funcionalidade de extensibilidade de IA](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) da Análise Dinâmica de Vídeo do Azure no Azure IoT Edge para implantar o modelo na borda e detectar caminhões de brinquedo em um fluxo de vídeo ao vivo.

Mostraremos como reunir o poder da Visão Personalizada para criar e treinar um modelo de pesquisa visual computacional carregando e rotulando algumas imagens. Você não precisa ter nenhum conhecimento em ciência de dados, machine learning ou IA. Você também conhecerá as funcionalidades da Análise Dinâmica de Vídeo para implantar com facilidade um modelo personalizado como um contêiner na borda e analisar um feed de vídeo ao vivo simulado.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Este tutorial mostra como:

> [!div class="checklist"]
> * Configurar os recursos relevantes.
> * Criar um modelo de Visão Personalizada na nuvem para detectar caminhões de brinquedo e implantá-lo na borda.
> * Criar e implantar um grafo de mídia com uma extensão HTTP em um modelo de Visão Personalizada.
> * Executar o código de exemplo.
> * Examinar e interpretar os resultados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

Leia os seguintes artigos antes de começar:

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Visão geral da Visão Personalizada do Azure](../../cognitive-services/custom-vision-service/overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md)
* [Análise de Vídeo ao vivo sem gravação de vídeo](analyze-live-video-concept.md)
* [Como executar a Análise Dinâmica de Vídeo com seu próprio modelo](use-your-model-quickstart.md)
* [Tutorial: Desenvolver um módulo do IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Como editar deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> O módulo de Visão Personalizada só dá suporte a **arquiteturas Intel x86 e amd64**. Verifique a arquitetura do seu dispositivo de borda antes de continuar.

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

Este tutorial usa um arquivo de [vídeo de inferência de carros de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para simular uma transmissão ao vivo. Você pode examinar o vídeo por meio de um aplicativo, como [player de mídia do VLC](https://www.videolan.org/vlc/). Selecione **Ctrl+N** e cole um link para o [vídeo de inferência de carros de brinquedo](https://lvamedia.blob.core.windows.net/public/t2.mkv) para iniciar a reprodução. Ao assistir ao vídeo, observe que, no marcador de 36 segundos, um caminhão de brinquedo aparece no vídeo. O modelo personalizado foi treinado para detectar esse caminhão de brinquedo específico. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

Neste tutorial, você usará a Análise Dinâmica de Vídeo no IoT Edge para detectar caminhões de brinquedo e publicar eventos de inferência associados no hub do IoT Edge.

## <a name="overview"></a>Visão geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagrama que mostra uma visão geral da Visão Personalizada.":::

O diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de extensão HTTP](media-graph-concept.md#http-extension-processor).

O nó de extensão HTTP desempenha a função de um proxy.  Ele faz uma amostragem dos quadros de vídeo de entrada definidos por você usando o campo `samplingOptions` e converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem por REST para outro módulo de borda que executa um modelo de IA por trás de um ponto de extremidade HTTP. Neste exemplo, esse módulo de borda é o modelo de detector de caminhão de brinquedos criado usando a Visão Personalizada. O nó de processador de extensão HTTP coleta os resultados da detecção e publica eventos no nó do [coletor do Hub IoT do Azure](media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [hub do IoT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Criar e implantar um modelo de detecção de brinquedo de Visão Personalizada 

Como o nome Visão Personalizada sugere, você pode usá-la para criar seu detector de objetos ou classificador personalizado na nuvem. Ela fornece uma interface simples, fácil de usar e intuitiva para criação de modelos de Visão Personalizada que podem ser implantados na nuvem ou na borda por meio de contêineres.

Para criar um detector de caminhões de brinquedo, siga as etapas do [Guia de Início Rápido: Criar um detector de objeto com o site da Visão Personalizada](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Esse módulo de Visão Personalizada dá suporte apenas a arquiteturas **Intel x86 e amd64**. Verifique a arquitetura do seu dispositivo de borda antes de continuar.

Observações adicionais:
 
* Para este tutorial, não use as imagens de exemplo fornecidas na [seção Pré-requisitos](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) do artigo de início rápido. Em vez disso, usamos um conjunto de imagens específico para criar um modelo de Visão Personalizada de detector de brinquedos. Use [estas imagens](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) quando for solicitado a [escolher suas imagens de treinamento](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) no guia de início rápido.
* Na seção de marcação de imagem do guia de início rápido, verifique se você está marcando o caminhão de brinquedo visto na imagem com a marca "caminhão de entrega".

Depois de terminar, exporte o modelo para um contêiner do Docker usando o botão **Exportar** na guia **Desempenho**. Escolha o Linux como o tipo de plataforma de contêiner. Esta é a plataforma na qual o contêiner será executado. O computador no qual você baixa o contêiner pode ser Windows ou Linux. As instruções a seguir foram baseadas no arquivo de contêiner baixado para um computador Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Tela que mostra o Dockerfile selecionado.":::
 
1. Você deve ter um arquivo zip baixado em seu computador local chamado `<projectname>.DockerFile.Linux.zip`. 
1. Verifique se você tem o Docker instalado. Caso contrário, instale o [Docker](https://docs.docker.com/get-docker/) na área de trabalho do Windows.
1. Descompacte o arquivo baixado em um local de sua escolha. Use a linha de comando para ir para o diretório de pasta descompactado.
    
    Execute os seguintes comandos:
    
    1. `docker build -t cvtruck` 
    
        Esse comando baixa muitos pacotes, compila a imagem do Docker e marca-a como `cvtruck:latest`.
    
        > [!NOTE]
        > Se ele for bem-sucedido, você verá as seguintes mensagens: `Successfully built <docker image id>` e `Successfully tagged cvtruck:latest`. Se o comando de build falhar, tente novamente. Às vezes, os pacotes de dependência não são baixados na primeira vez.
    1. `docker  image ls`

        Esse comando verifica se a nova imagem está no Registro local.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Esse comando deverá publicar a porta exposta do Docker (80) na porta do computador local (80).
    1. `docker container ls`
    
        Esse comando verifica os mapeamentos de porta e se o contêiner do Docker está sendo executado com êxito no seu computador. A saída deve ser algo como:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Esse comando testa o contêiner no computador local. Se a imagem tiver o mesmo caminhão de entrega no qual treinamos o modelo, a saída será parecida com a do exemplo a seguir. Isso sugere que o caminhão de entrega foi detectado com uma probabilidade de 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Examinar os arquivos de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implantar o manifesto de implantação

1. No Visual Studio Code, navegue até src/cloud-to-device-console-app/operations.json.

1. Em `GraphTopologySet`, verifique se o seguinte é verdadeiro:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. Em `GraphInstanceSet`, verifique o seguinte:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Adicione o seguinte conteúdo à parte superior da matriz de parâmetros: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Altere o valor do parâmetro `rtspUrl` para `"rtsp://rtspsim:554/media/t2.mkv"`.
1. Em `GraphTopologyDelete`, verifique `"name": "InferencingWithHttpExtension"`.
1. Clique com o botão direito do mouse no arquivo src/edge/ deployment.customvision.template.json e selecione **Gerar o Manifesto de Implantação do IoT Edge**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Captura de tela que mostra a opção Gerar o Manifesto de Implantação do IoT Edge.":::
  
    Essa ação deverá criar um arquivo de manifesto na pasta src/edge/config chamado deployment.customvision.amd64.json.
1. Abra o arquivo src/edge/deployment.customvision.template.json e localize o bloco JSON `registryCredentials`. Nesse bloco, você encontrará o endereço do seu Registro de Contêiner do Azure junto com o nome de usuário e a senha.
1. Envie por push o contêiner local de Visão Personalizada para a instância do Registro de Contêiner do Azure seguindo estas etapas na linha de comando:

    1. Entre no registro executando o seguinte comando:
    
        `docker login <address>`
    
        Insira o nome de usuário e a senha quando precisar se autenticar.
        
        > [!NOTE]
        > A senha não está visível na linha de comando.
    1. Marque a imagem usando este comando: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Envie a imagem por push usando este comando: <br/>`docker push <address>/cvtruck`.

        Se isso for bem-sucedido, você verá `Pushed` na linha de comando junto com o SHA da imagem.
    1. Confirme isso também verificando a instância do Registro de Contêiner do Azure no portal do Azure. Aqui, você verá o nome do repositório junto com a marca.
1. Defina a cadeia de conexão do Hub IoT selecionando o ícone **Mais ações** ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo. Você pode copiar a cadeia de caracteres do arquivo appsettings.json. (Esta é outra abordagem recomendada para verificar se você tem o hub IoT correto configurado no Visual Studio Code por meio do [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Captura de tela que mostra a opção Definir Cadeia de Conexão do Hub IoT.":::
1. Em seguida, clique com o botão direito do mouse em src/edge/config/deployment.customvision.amd64.json e escolha **Criar Implantação para Dispositivo Único**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Captura de tela que mostra a opção Criar Implantação para Dispositivo Único.":::
1. Em seguida, você precisará selecionar um dispositivo Hub IoT. Selecione **lva-sample-device** na lista suspensa.
1. Em cerca de 30 segundos, atualize o Hub IoT do Azure no canto inferior esquerdo. Você deverá ter o dispositivo de borda com os seguintes módulos implantados:

    * O módulo da Análise Dinâmica de Vídeo no IoT Edge chamado `lvaEdge`.
    * Um módulo chamado `rtspsim`, simulando um servidor RTSP que funciona como a origem de um feed de vídeo ao vivo.
    * Um módulo chamado `cv`, que, como o nome sugere, é o modelo de detecção de caminhões de brinquedo de Visão Personalizada que aplica a Visão Personalizada às imagens e retorna vários tipos de marcas. (Nosso modelo foi treinado em apenas uma marca: caminhão de entrega).

## <a name="prepare-for-monitoring-events"></a>Preparar-se para eventos de monitoramento

Clique com o botão direito do mouse no dispositivo da Análise Dinâmica de Vídeo e selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela **SAÍDA** do Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Captura de tela que mostra a opção Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno.":::

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

Se você abrir a topologia do grafo deste tutorial em um navegador, verá que o valor de `inferencingUrl` foi definido como `http://cv:80/image`. Essa configuração significa que o servidor de inferência retornará os resultados depois de detectar os caminhões de brinquedo, se houver, no vídeo ao vivo.

1. No Visual Studio Code, abra a guia **Extensões** (ou selecione **CTRL+SHIFT+X**) e procure Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Captura de tela que mostra a opção Configurações da Extensão.":::
1. Procure e habilite **Mostrar Mensagem Detalhada**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Captura de tela que mostra a opção Mostrar Mensagem Detalhada.":::
1. Para iniciar uma sessão de depuração, selecione a tecla **F5**. Você verá mensagens impressas na janela **TERMINAL**.
1. O código operations.json começa fazendo chamadas para os métodos diretos `GraphTopologyList` e `GraphInstanceList`. Se você limpar os recursos após a conclusão dos guias de início rápido anteriores, esse processo retornará listas vazias e, depois, será pausado. Para continuar, selecione a tecla **ENTER**.
    
   A janela **TERMINAL** mostra o próximo conjunto de chamadas de método direto:
    
   * Uma chamada a `GraphTopologySet` que usa a `topologyUrl` anterior.
   * Uma chamada para `GraphInstanceSet` que usa o seguinte corpo:
        
   ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Uma chamada a `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo.
   * Uma segunda chamada a `GraphInstanceList` que mostra que a instância do grafo está no estado de execução.
    
1. A saída na janela **TERMINAL** é pausada no prompt **Clique em ENTER para continuar**. Não selecione **ENTER** ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste tutorial aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte à janela **TERMINAL** e selecione **ENTER**.
A próxima série de chamadas limpa os recursos:
    
   * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
   * Uma chamada para `GraphInstanceDelete` exclui a instância.
   * Uma chamada para `GraphTopologyDelete` exclui a topologia.
   * Uma chamada final para `GraphTopologyList` mostra que a lista está vazia.
    
## <a name="interpret-the-results"></a>Interpretar os resultados

Quando você executa o grafo de mídia, os resultados do nó do processador de extensão HTTP passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela de **SAÍDA** contêm uma seção de corpo e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso. O tipo de evento é **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Nesta mensagem, observe estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o assunto) se conectou ao simulador RTSP e começou a receber um feed ao vivo simulado.
* Em `applicationProperties`, `subject` indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em `applicationProperties`, o tipo de evento indica que esse é um evento de diagnóstico.
* A hora do evento indica a hora em que o evento ocorreu.
* O corpo contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó do processador de extensão HTTP recebe resultados de inferência do contêiner de Visão Personalizada e emite os resultados por meio do nó do coletor do Hub IoT como eventos de inferência.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Observe as seguintes informações nas mensagens anteriores:

* O assunto em `applicationProperties` referencia o nó do MediaGraph no qual a mensagem foi gerada. Nesse caso, a mensagem é originada do processador de extensão HTTP.
* O tipo de evento em `applicationProperties` indica que esse é um evento de inferência de análise.
* A hora do evento indica a hora em que o evento ocorreu.
* O corpo contém dados sobre o evento de análise. Nesse caso, o evento é de inferência e, portanto, o corpo contém uma matriz de inferências chamada previsões.
* A seção de previsões contém uma lista de previsões em que o caminhão de entrega de brinquedo (a marca é "caminhão de entrega") é encontrado no quadro. Como você se lembra, "caminhão de entrega" é a marca personalizada que você forneceu ao modelo treinado personalizado para o caminhão de brinquedo. O modelo faz uma inferência e identifica o caminhão de brinquedo no vídeo de entrada com diferentes pontuações de confiança de probabilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender experimentar outros tutoriais ou guias de início rápido, mantenha os recursos criados. Caso contrário, acesse o portal do Azure, navegue até os grupos de recursos, selecione o grupo de recursos no qual você executou este tutorial e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Pesquise pelas câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 em vez de usar uma VM Linux do Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md).

Depois, registre o dispositivo no Hub IoT do Azure seguindo as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md).