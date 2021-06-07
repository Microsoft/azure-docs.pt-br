---
title: O que é a extensão de grafo de mídia – Azure
description: A Análise Dinâmica de Vídeo no IoT Edge permite que você estenda as funcionalidades de processamento do grafo de mídia por meio de um nó de extensão de grafo.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 4a7aea7cc60a67603d8a0376cf84228072659d6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557608"
---
# <a name="media-graph-extension"></a>Extensão do grafo de mídia

A Análise Dinâmica de Vídeo no IoT Edge permite que você estenda as funcionalidades de processamento do grafo de mídia por meio de um nó de extensão de grafo. O plug-in de extensão de análise pode fazer uso de técnicas tradicionais de processamento de imagem ou de modelos de IA de pesquisa visual computacional. As extensões de grafo são habilitadas pela inclusão de um nó de processador de extensão em um grafo de mídia. O nó do processador de extensão retransmite os quadros de vídeo para o ponto de extremidade configurado e funciona como a interface para a extensão. A conexão pode ser feita com um ponto de extremidade local ou remoto e pode ser protegida por autenticação e criptografia TLS, se necessário. Além disso, o nó do processador de extensão de grafo permite o dimensionamento e a codificação opcionais dos quadros de vídeo antes que eles sejam enviados para a sua extensão personalizada. 

A Análise Dinâmica de Vídeo dá suporte a dois tipos de processadores de extensão de grafo de mídia:

* [Processador de extensão HTTP](media-graph-concept.md#http-extension-processor)
* [Processador de extensão gRPC](media-graph-concept.md#grpc-extension-processor)

O nó de extensão de grafo espera que o plug-in de extensão de análise retorne os resultados no formato JSON. O ideal é que os resultados sigam o [modelo de objeto de esquema de metadados de inferência](./inference-metadata-schema.md).

## <a name="http-extension-processor"></a>Processador de extensão HTTP

O processador de extensão HTTP permite cenários de extensibilidade usando o [protocolo HTTP](./http-extension-protocol.md), no qual o desempenho e/ou a utilização de recursos ideal não é a principal preocupação. Você pode expor a própria IA para um grafo de mídia por meio de um ponto de extremidade HTTP REST. 

Use o nó do processador de extensão HTTP quando:

* Você quiser ter uma melhor interoperabilidade com os sistemas de inferência HTTP existentes.
* A transferência de dados de baixo desempenho for aceitável.
* Você desejar usar uma interface simples de solicitação-resposta para a Análise Dinâmica de Vídeo.

## <a name="grpc-extension-processor"></a>Processador de extensão gRPC

O processador de extensão gRPC permite cenários de extensibilidade usando o [protocolo estruturado altamente funcional baseado em gRPC](./grpc-extension-protocol.md). É ideal para cenários em que o desempenho e/ou a utilização de recursos ideal é uma prioridade. O processador de extensão gRPC permite que você obtenha todos os benefícios das definições de dados estruturados. O gRPC oferece alto desempenho de transferência de conteúdo usando:

* [a memória compartilhada nativa](https://en.wikipedia.org/wiki/Shared_memory) ou 
* inserindo diretamente o conteúdo no corpo de mensagens gRPC. 

O processador de extensão gRPC pode ser usado para enviar propriedades de mídia junto com a troca de mensagens de inferência.
Portanto, use um nó do processador de extensão gRPC quando:

* Desejar usar um contrato estruturado (por exemplo, mensagens estruturadas para solicitações e respostas)
* Desejar usar buffers de protocolo ([protobuf](https://developers.google.com/protocol-buffers)) como o formato de intercâmbio de mensagens subjacente para comunicação.
* Desejar se comunicar com um servidor gRPC em uma só sessão de fluxo, em vez do modelo tradicional de solicitação-resposta que precisa de um manipulador de solicitação personalizado para analisar as solicitações de entrada e chamar as funções de implementação corretas. 
* Desejar obter uma comunicação de baixa latência e alta taxa de transferência entre a Análise Dinâmica de Vídeo e o módulo.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Usar o modelo inferência com a Análise Dinâmica de Vídeo

As extensões de grafo de mídia permitem executar modelos de inferência de sua escolha em qualquer runtime de inferência disponível, como o ONNX, o TensorFlow, o PyTorch ou outros, no próprio contêiner do Docker. A extensão personalizada de inferência deve ser implantada junto com o módulo de borda da Análise Dinâmica de Vídeo para melhor desempenho e será então invocada por meio do processador de extensão HTTP ou gRPC incluído na topologia de grafo. Além disso, a frequência das chamadas na extensão personalizada pode ser restringida por meio da adição opcional de um [processador de detector de movimento](media-graph-concept.md#motion-detection-processor) upstream ao processador de extensão de mídia.

O diagrama abaixo descreve o fluxo de dados de alto nível:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="Serviço de inferência de IA":::

## <a name="samples"></a>Exemplos

Comece com um dos nossos guias de início rápido que ilustram a Análise Dinâmica de Vídeo com o serviço de extensão predefinido com baixas taxas de quadros com o [processador de extensão HTTP](./use-your-model-quickstart.md?pivots=programming-language-csharp) ou com altas taxas de quadros com o [processador de extensão gRPC](./analyze-live-video-use-your-grpc-model-quickstart.md?pivots=programming-language-csharp)

Para usuários avançados, confira alguns dos nossos exemplos de [Jupyter Notebook](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) para a Análise Dinâmica de Vídeo. Estes notebooks fornecerão instruções passo a passo sobre **as extensões do grafo de mídia** em:

* Como criar uma imagem de contêiner do Docker de um serviço de extensão
* Como implantar o serviço de extensão como um contêiner junto com o contêiner da Análise Dinâmica de Vídeo
* Como usar um grafo de mídia da Análise Dinâmica de Vídeo com um cliente de extensão e apontá-lo para o ponto de extremidade da extensão (HTTP/gRPC)