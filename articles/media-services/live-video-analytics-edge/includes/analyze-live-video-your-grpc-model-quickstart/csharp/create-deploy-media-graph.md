---
ms.openlocfilehash: 8a9149119bc754ff0715f2841925da01301faecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101749995"
---
### <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo

Como parte dos pré-requisitos, você baixou o código de exemplo para uma pasta. Siga estas etapas para examinar e editar os arquivos de exemplo.

1. No Visual Studio Code, vá até *src/edge*. Você verá seu arquivo *.env* e alguns arquivos do modelo de implantação.

    O deployment.grpcyolov3icpu.template.json se refere ao manifesto de implantação do dispositivo de borda. Ele inclui alguns valores de espaço reservado. O arquivo .env inclui os valores dessas variáveis.
1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:
    
    * c2d-console-app.csproj – o arquivo de projeto para o Visual Studio Code.
    * operations.json – uma lista das operações que você deseja que o programa execute.
    * Program.cs – o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus métodos diretos.
        * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos que foram gerados pelo módulo na janela **SAÍDA**.
        * Invoca métodos diretos para limpar recursos.
1. Edite o arquivo *operations.json*:
 
    * Altere o link para a topologia do grafo:
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-with-grpcExtension/2.0/topology.json"`
    * Em GraphInstanceSet, edite o nome da topologia de grafo para que corresponda ao valor no link anterior:
    * `"topologyName"` : `"EVROnMotionPlusGrpcExtension"`
    * Em GraphTopologyDelete, edite o nome:
    * `"name"` : `"EVROnMotionPlusGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>Expanda isso e confira como o nó MediaGraphGrpcExtension é implementado na topologia</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "256"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

1. Clique com o botão direito do mouse no arquivo *src/edge/* *deployment.grpcyolov3icpu.template.json* e selecione **Gerar um Manifesto de Implantação do IoT Edge**.

    ![Gerar um manifesto de implantação do IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    O arquivo de manifesto *deployment.grpcyolov3icpu.amd64.json* será criado na pasta *src/edge/config*.
1. Se você concluiu o início rápido [Detectar movimento e emitir eventos](../../../detect-motion-emit-events-quickstart.md), ignore esta etapa.

    Caso contrário, ao lado do painel **HUB IOT DO AZURE** no canto inferior esquerdo, selecione o ícone **Mais ações** e, em seguida, selecione **Definir Cadeia de Conexão do Hub IoT**. Você pode copiar a cadeia de caracteres do arquivo *appsettings.json*. Ou, para garantir que configurou o Hub IoT adequado dentro do Visual Studio Code, use o [comando Selecionar Hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    ![Cadeia de conexão do Hub IoT](../../../media/quickstarts/iot-hub-connection-string-grpc.png)

    > [!NOTE]
    > Talvez você precise fornecer informações do ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será algo parecido com isto:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Clique com o botão direito do mouse em *src/edge/config/* *deployment.grpcyolov3icpu.amd64.json* e selecione **Criar Implantação para Dispositivo Único**.

    ![criar implantação para dispositivo único](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. Quando receber um prompt para selecionar um dispositivo do Hub IoT, selecione **lva-sample-device**.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:

    * O módulo da Análise Dinâmica de Vídeo, chamado **lvaEdge**.
    * O módulo **rtspsim**, que simula um servidor RTSP e funciona como a origem de um feed de vídeo ao vivo.

        > [!NOTE]
        > As etapas acima pressupõem que você esteja usando a máquina virtual criada pelo script de instalação. Se estiver usando um dispositivo de borda próprio, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador** para efetuar pull do arquivo de vídeo de exemplo usado neste guia de início rápido e armazená-lo:  

        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
    * O módulo **lvaExtension**, o modelo de detecção de objetos YOLOv3 que usa o gRPC como o método de comunicação e aplica a pesquisa visual computacional às imagens e retorna várias classes de tipos de objetos.
    
    ![Extensão lva](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>Preparar-se para monitorar eventos

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Mostrar Mensagem Detalhada":::
1. Clique com o botão direito do mouse no dispositivo de Análise Dinâmica de Vídeo e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**. Essa etapa é necessária para monitorar os eventos do Hub IoT na janela **SAÍDA** do Visual Studio Code.

   ![Começar a monitorar](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

    > [!NOTE]
    > Talvez você precise fornecer informações sobre o ponto de extremidade interno para o Hub IoT. Para obter essas informações, no portal do Azure, navegue até o Hub IoT e procure a opção **Pontos de extremidade internos** no painel de navegação à esquerda. Clique nele e procure o **Ponto de extremidade compatível com hub de eventos** na seção **Ponto de extremidade compatível com hub de eventos**. Copie e use o texto na caixa. O ponto de extremidade será parecido com este:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
### <a name="run-the-sample-program"></a>Executar o programa de exemplo

1. Para iniciar uma sessão de depuração, pressione F5. Você verá mensagens impressas na janela TERMINAL.
1. O código *operations.json* começa fazendo chamadas para os métodos diretos `GraphTopologyList` e `GraphInstanceList`. Se você limpou os recursos após a conclusão dos inícios rápidos anteriores, esse processo retornará listas vazias e, em seguida, pausará. Para continuar, pressione Enter.
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "2.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    A janela **TERMINAL** mostra o próximo conjunto de chamadas de método direto:
    
    * Uma chamada a `GraphTopologySet` que usa a topologyUrl anterior
    * Uma chamada para `GraphInstanceSet` que usa o seguinte corpo:
    
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "EVROnMotionPlusGrpcExtension",
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
    
    * Uma chamada a `GraphInstanceActivate` que inicia a instância do grafo e o fluxo de vídeo.
    * Uma segunda chamada a `GraphInstanceList` que mostra que a instância do grafo está no estado de execução.
1. A saída na janela **TERMINAL** é pausada no aviso Clique em ENTER para continuar. Não pressione Enter ainda. Role para cima para ver os conteúdos da resposta JSON para os métodos diretos que você invocou.
1. Alterne para a janela **SAÍDA** no Visual Studio Code. Você verá mensagens que o módulo da Análise Dinâmica de Vídeo no IoT Edge está enviando para o Hub IoT. A próxima seção deste início rápido aborda essas mensagens.
1. O grafo de mídia continua sendo executado e imprimindo resultados. O simulador RTSP mantém o loop do vídeo de origem. Para interromper o grafo de mídia, volte para a janela **TERMINAL** e selecione Enter.
1. A próxima série de chamadas limpa os recursos:
    
    * Uma chamada para `GraphInstanceDeactivate` desativa a instância do grafo.
    * Uma chamada para `GraphInstanceDelete` exclui a instância.
    * Uma chamada para `GraphTopologyDelete` exclui a topologia.
    * Uma chamada final para `GraphTopologyList` mostra que a lista está vazia.

