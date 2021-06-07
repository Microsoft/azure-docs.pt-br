---
title: Desenvolver e implantar um servidor de inferência gRPC-Azure
description: Este artigo fornece orientação sobre como desenvolver e implantar um servidor de inferência gRPC.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034853"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Guia de instruções – desenvolver e implantar um servidor de inferência de gRPC

## <a name="overview"></a>Visão geral

Este artigo mostra como você pode encapsular os modelos de ia de sua escolha em um servidor de inferência gRPC, para que ele possa ser integrado à LVA (análise de vídeo ao vivo) por meio da extensão do grafo. 

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Extensões de grafo de mídia](media-graph-extension-concept.md)
* [Protocolo de extensão gRPC](grpc-extension-protocol.md)
* [Esquema de metadados de inferência](inference-metadata-schema.md)
* [Introdução ao gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Guia de linguagem Proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo x86-64 ou ARM64 que executa um dos [sistemas operacionais Linux com suporte](../../iot-edge/support.md#operating-systems) ou um computador Windows.
* [Instale o Docker](https://docs.docker.com/desktop/#download-and-install) em seu computador.
* Instale o [IOT Edge Runtime](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>etapas de implementação do gRPC

Para criar um servidor de inferência gRPC e implementá-lo como uma extensão com análise de vídeo ao vivo, as etapas a seguir serão usadas:

### <a name="setup"></a>Configuração:

Execute as etapas necessárias para que o [módulo análise de vídeo ao vivo seja implantado e funcione em um dispositivo IOT Edge](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Etapas de implementação de alto nível:

1. Escolha uma das várias linguagens com suporte do gRPC: C#, C++, Dart, go, Java, Node, Objective-C, PHP, Python, Ruby.
1. Implemente um servidor gRPC que se comunicará com a análise de vídeo ao vivo usando [os arquivos Proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="Servidor gRPC que se comunicará com a análise de vídeo ao vivo usando os arquivos Proto3":::

    Dentro deste serviço:
    1. Manipular a troca de mensagens de descrição de sessão entre o servidor e o cliente.
    1. Manipule [mensagens de exemplo de mídia](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) e retorne os resultados.

        1. Invoque seu mecanismo de inferência que usa um modelo treinado para fazer inferências nas mensagens de entrada.
        1. Receba os resultados do inferência do mecanismo, empacote-os novamente como um exemplo de mídia e envie de volta para a análise de vídeo ao vivo usando o arquivo [inferência. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) .

            Como alternativa, invoque qualquer função de transformação de mídia para o exemplo de mídia.
1. Implante a implementação do servidor gRPC. Há duas maneiras de fazer isso:

    1. Implantar como um módulo IoT colocalizado com o módulo análise de vídeo ao vivo
    1. Implante como um módulo IoT em um nó acessível à rede (no local ou na nuvem) que pode trocar dados com o módulo análise de vídeo ao vivo.
1. Configure uma topologia de grafo de análise de vídeo ao vivo com o módulo análise de vídeo ao vivo e aponte-o para o servidor gRPC.

### <a name="recommendation"></a>Recomendação:

Ao posicionar no mesmo nó, a memória compartilhada pode ser usada para melhorar o desempenho. Isso exige que você use recursos de memória compartilhada do Linux expostos pelo ambiente/linguagem de programação.

1. Abra o identificador de memória compartilhada do Linux.
1. Após o recebimento de um quadro, acesse o deslocamento de endereço dentro da memória compartilhada.
1. Confirme a conclusão do processamento de quadros para que sua memória possa ser recuperada pela análise de vídeo ao vivo.

## <a name="create-a-grpc-inference-server"></a>Criar um servidor de inferência de gRPC

Agora, você criará um módulo de IoT Edge (ia externo) que aceita quadros de vídeo da análise de vídeo ao vivo usando mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) por meio de memória compartilhada, classifica os quadros como "escuro" ou "leve" e retorna resultados de inferência para o coletor de mensagens do Hub IOT na análise de vídeo ao vivo usando o [esquema de metadados de inferência](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="criar um módulo de IoT Edge (ia externo)":::

Esse servidor de inferência de gRPC é um aplicativo de console .NET Core criado para lidar com as mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) enviadas entre a análise de vídeo ao vivo e o ia personalizado. A seguir está o fluxo de mensagens entre a análise de vídeo ao vivo e o servidor de inferência de gRPC:

1. A análise de vídeo ao vivo envia um descritor de fluxo de mídia (consulte [extensão. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)) que define as informações de fluxo de mídia que serão enviadas seguidas por quadros de vídeo para o servidor como uma mensagem [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) pela sessão de fluxo gRPC. 
1. O servidor valida e confirma o descritor de fluxo e configura o método de transferência de dados desejado.
1. A análise de vídeo ao vivo começa a enviar os arquivos MediaSample que contêm os quadros de vídeo.
1. O servidor analisa os quadros de vídeo à medida que recebe e começa a processá-los usando um processador de imagem definido por você.
1. O servidor, em seguida, retorna resultados de inferência como mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) assim que elas estão disponíveis. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Criar um servidor de inferência de gRPC":::

Os quadros de vídeo podem ser transferidos por meio da [memória compartilhada](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) ou podem ser inseridos na mensagem protobuf. O modo de transferência de dados pode ser configurado na topologia do grafo LVA para determinar como os quadros serão transferidos. Isso é conseguido com a configuração do elemento **DataTransfer** da propriedade MediaGraphGrpcExtension, conforme mostrado abaixo:

Caractere

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Memória compartilhada:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Ao se comunicar pela memória compartilhada, o valor de IpcMode deve ser definido como **compartilhável** e, no módulo de servidor gRPC, defina o valor de IpcMode para **contêiner: {CONTAINER_NAME}**. Essas configurações devem ser feitas no arquivo de manifesto de implantação que é usado para implantar os módulos no Hub IoT do Azure. Abaixo está um exemplo das opções de contêiner a serem usadas ao configurar os módulos de IoT Edge.

Módulo de análise de vídeo ao vivo:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

módulo de extensão gRPC:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Verifique se você pode acessar a área de memória compartilhada de "Container: lvaEdge" dentro do grpcExtension.

## <a name="sample-grpc-server"></a>Servidor gRPC de exemplo

Para entender os detalhes de como o gRPC Server é desenvolvido, vamos percorrer nosso exemplo de código.

1. Clone o repositório do link do GitHub [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Inicie o VSCode e navegue até a pasta/src/edge/modules/grpcExtension.
1. Vamos fazer uma rápida explicação dos arquivos:

    1. **Program. cs**: esse é o ponto de entrada do aplicativo. Ele é responsável por inicializar e gerenciar o servidor gRPC, que atuará como um host. Em nosso exemplo, a porta para escutar mensagens gRPC de entrada de um cliente gRPC (como análise de vídeo ao vivo) em é especificada pelo elemento de configuração grpcBindings no AppConfig.jsem.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: essa classe é responsável por manipular as mensagens [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) . Ele lerá o quadro na mensagem, invocará o ImageProcessor e gravará os resultados da inferência.
Agora que configuramos e inicializamos as conexões de porta do servidor gRPC, vamos examinar como podemos processar as mensagens gRPC de entrada.

        * Depois que uma sessão gRPC é estabelecida, a primeira mensagem que o servidor gRPC receberá do cliente (análise de vídeo ao vivo) é um MediaStreamDescriptor que é definido no arquivo [extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) . 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * Em nossa implementação de servidor, o método `ProcessMediaStreamDescriptor` validará a propriedade MediaDescriptor do MediaStreamDescriptor para um arquivo de vídeo e, em seguida, configurará o modo de transferência de dados (que está usando a memória compartilhada ou usando o modo de transferência de quadro incorporado) dependendo do que você especificar na topologia e do arquivo de modelo de implantação usado. 
        * Ao receber a mensagem e configurar com êxito o modo de transferência de dados, o servidor gRPC retorna a mensagem MediaStreamDescriptor de volta ao cliente como uma confirmação e, portanto, estabelece uma conexão entre o servidor e o cliente.    
        * Após a análise de vídeo ao vivo receber a confirmação, ela começará a transferir o fluxo de mídia para o servidor gRPC. Em nossa implementação de servidor, o método `ProcessMediaStream` processará o MediaStreamMessage de entrada. O MediaStreamMessage também é definido na [extensão. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Dependendo do valor de batchSize no Appconfig.jsem, nosso servidor continuará recebendo as mensagens e armazenará os quadros de vídeo em uma lista. Depois que o limite de batchSize for atingido, a função chamará a função ou o arquivo que processará a imagem. Em nosso caso, o método chama um arquivo chamado BatchImageProcessor. cs
    1. **Processors\BatchImageProcessor.cs**: essa classe é responsável por processar a (s) imagem (ões). Nós usamos um modelo de classificação de imagem neste exemplo. Para cada imagem que será processada, o algoritmo usado é o seguinte:

        1. Converta a imagem em uma matriz de bytes para processamento. Consulte o método: `GetBytes(Bitmap image)`
        
            O processador de exemplo que estamos usando oferece suporte apenas ao quadro de imagem codificada JPG e nenhum como formato de pixel. Caso o processador personalizado dê suporte a uma codificação e/ou formato diferente, atualize o `IsMediaFormatSupported` método da classe Processor.
        1. Usando a [classe ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1), converta a imagem em escala de cinza. Consulte o método: `ToGrayScale(Image source)` .
        1. Assim que obtivermos a imagem de escala cinza, calcularemos a média dos bytes de escala cinza.
        1. Se o valor médio < 127, então classificamos a imagem como "Dark", senão classificaremos como "Light" com o valor de confiança como 1,0. Consulte o método: `ProcessImage(List<Image> images)` .

    Você pode adicionar sua própria lógica de processador modificando essa classe ou adicionando uma nova classe e implementando o método:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Depois de adicionar a nova classe, você precisará atualizar o MediaGraphExtensionService. cs para que ele instancie sua classe e invoque o método ProcessImage nela para executar a lógica de processamento. 

## <a name="connect-with-live-video-analytics-module"></a>Conectar-se ao módulo análise de vídeo ao vivo

Agora que você criou o módulo de extensão gRPC, agora criaremos e implantaremos a topologia de mídia gráfica.

1. Usando o Visual Studio Code, siga [estas instruções](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) para entrar no Docker.
1. No Visual Studio Code, vá até src/edge. Você verá seu arquivo .env e alguns arquivos do modelo de implantação.

    O modelo de implantação se refere ao manifesto de implantação do dispositivo de borda. Ele inclui alguns valores de espaço reservado. O arquivo .env inclui os valores dessas variáveis.
    
    Em seguida, selecione Compilar e efetuar push da solução do IoT Edge. Use src/Edge/deployment.grpc.template.jsem para esta etapa.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Conectar-se ao módulo análise de vídeo ao vivo":::
    
    Essa ação cria o módulo do servidor grpc e envia a imagem para o registro de contêiner do Azure.
    Verifique se há as variáveis de ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr definidas no arquivo .env.
1. Vá até a pasta src/cloud-to-device-console-app. Aqui, você vê o arquivo appsettings.json e alguns outros arquivos:

    * c2d-console-app.csproj – o arquivo de projeto para o Visual Studio Code.
    * operations.json – uma lista das operações que você deseja que o programa execute.
    * Program.cs – o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](direct-methods.md).
        * Pausa para você examinar a saída do programa na janela TERMINAL e os eventos que foram gerados pelo módulo na janela SAÍDA.
        * Invoca métodos diretos para limpar recursos.
1. Edite o arquivo operations.json:

    * Altere o link para a topologia do grafo:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * Em GraphInstanceSet, edite o nome da topologia de grafo para que corresponda ao valor no link anterior:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * Em GraphTopologyDelete, edite o nome:<br/>`"name": "InferencingWithGrpcExtension"`

            A topologia (por exemplo, `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) deve definir um endereço de extensão:
    * Parâmetro de endereço de extensão

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Configuração

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Gerar e implantar o manifesto de implantação do IoT Edge

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda e as definições de configuração para esses módulos. Siga estas etapas para gerar um manifesto no arquivo de modelo e, em seguida, implantá-lo no dispositivo de borda.
Esta etapa cria o manifesto de implantação IoT Edge em src/Edge/config/deployment.grpc.amd64.jsem. Clique com o botão direito do mouse nesse arquivo e selecione **criar implantação para um único dispositivo**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Gerar e implantar o manifesto de implantação do IoT Edge":::

Em seguida, o Visual Studio Code solicitará que você selecione um dispositivo do Hub IoT. Selecione seu dispositivo do IoT Edge, que deve ser lva-sample-device.
Nesta fase, a implantação dos módulos de borda em seu dispositivo do IoT Edge já foi iniciada. Em cerca de 30 segundos, atualize o Hub IoT do Azure no canto inferior esquerdo no Visual Studio Code. Você deve ver que um novo módulo foi implantado chamado lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Um novo módulo foi implantado chamado lvaExtension":::

## <a name="next-steps"></a>Próximas etapas

Siga as etapas de **preparação para monitorar eventos** mencionadas no [vídeo de análise ao vivo com seu modelo](use-your-model-quickstart.md) de início rápido para executar o exemplo e interpretar os resultados. Além disso, confira nossas gRPC topologias de exemplo: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension e [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).