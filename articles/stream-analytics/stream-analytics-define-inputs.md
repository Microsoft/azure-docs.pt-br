---
title: "Conexão de dados: entradas de transmissão de dados de uma transmissão de eventos | Microsoft Docs"
description: "Saiba mais sobre como configurar uma conexão de dados com o Stream Analytics chamada de “entradas”. As entradas incluem um fluxo de dados de eventos e também dados de referência."
keywords: "fluxo de dados, conexão de dados, fluxo de eventos"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/05/2017
ms.author: samacha
ms.openlocfilehash: f5a605e0b0809c27feedc98390175fd383a371eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Conexão de dados: saiba mais sobre entradas de fluxo de dados de eventos para o Stream Analytics
A conexão de dados para um trabalho do Stream Analytics é um fluxo de eventos de uma fonte de dados, que é conhecido como a *entrada* de um trabalho. O Stream Analytics tem integração de primeira classe com fontes de fluxo de dados do Azure, incluindo [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) e [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/). Essas fontes de entrada podem ser da mesma assinatura do Azure que o trabalho de análise ou de uma assinatura diferente.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de entrada de dados: fluxo de dados e dados de referência
Como os dados são enviados a uma fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real. As entradas são divididas em dois tipos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-inputs"></a>Entradas de fluxo de dados
Um fluxo de dados é uma sequência ilimitada de eventos ao longo do tempo. Os trabalhos do Stream Analytics devem conter pelo menos uma fonte de entrada de fluxo de dados. O Armazenamento de Blobs, os Hubs de Eventos e o Hub IoT têm suporte como fontes de entrada de fluxo de dados. Os hubs de Eventos são usados para coletar fluxos de eventos de vários dispositivos e serviços. Esses fluxos podem incluir os feeds de atividades de mídia social, informações de mercado de ações ou dados de sensores. Os hubs IoT são otimizados para coletar dados de dispositivos conectados em cenários da Internet das Coisas (IoT).  O Armazenamento de Blobs pode ser usado como uma fonte de entrada para ingerir dados em massa como uma transmissão, tais como arquivos de log.  

### <a name="reference-data"></a>Dados de referência
O Stream Analytics também dá suporte à entrada conhecida como *dados de referência*. Esses são dados auxiliares que são estáticos ou que são alterados lentamente. Ela é normalmente usada para executar correlação e pesquisas. Por exemplo, você pode unir dados na entrada de fluxo de dados a dados nos dados de referência, assim como você realizaria uma junção SQL para pesquisar valores estáticos. O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 100 MB de tamanho.

Para saber como criar entradas de dados de referência, veja [Usar dados de referência](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Compactação

O Azure Stream Analytics em breve implantará um recurso de compactação em todas as fontes de entrada de fluxo de dados (armazenamento de blobs, Hub IoT e Hubs de Eventos). Este recurso adiciona uma nova opção de lista suspensa à folha **Nova entrada** no Portal do Azure, permitindo que você opte por compactar os fluxos de dados. Os tipos de compactação com suporte no momento são Nenhuma, GZip e Deflate. 

A compactação não tem suporte em combinação com a serialização Avro e não é aplicável a dados de referência. 

## <a name="create-data-stream-input-from-event-hubs"></a>Criar entrada de fluxo de dados dos Hubs de Eventos

Os Hubs de Eventos do Azure fornecem ingestores de eventos altamente escalonável de publicação/assinatura. Um hub de eventos pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Juntos, os Hubs de Eventos e o Stream Analytics oferecem a você uma solução ponta a ponta para análise em tempo real – os Hubs de Eventos permitem que você alimente eventos no Azure em tempo real e os trabalhos do Stream Analytics podem processar esses eventos em tempo real. Por exemplo, você pode enviar cliques da Web, leituras do sensor ou eventos de log online para Hubs de Eventos. Em seguida, você pode criar trabalhos do Stream Analytics para usar Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

O carimbo de data/hora padrão de eventos provenientes dos Hubs de Eventos no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub de Eventos, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Grupos de consumidores
Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou ele tem várias entradas, algumas entradas podem ser lidas por mais de um leitor de downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics. Também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter mais informações, confira o [Guia de programação de Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Configurar um Hub de Eventos como um fluxo de dados de entrada
A tabela a seguir explica cada propriedade na folha **Nova Entrada** no Portal do Azure, quando você configura um Hub de Eventos como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Namespace do barramento de serviço** |Um namespace do Barramento de Serviço do Azure, que é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também cria um namespace do Barramento de Serviço. |
| **Nome do Hub de Eventos** |O nome do Hub de Eventos para usar como entrada. |
| **Nome da política do Hub de Eventos** |A política de acesso compartilhado que fornece acesso ao Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| **Grupo de consumidores de Hub de Eventos** (opcional) |O grupo de consumidores para ingerir dados do Hub de Eventos. Se nenhum grupo de consumidores for especificado, o trabalho do Stream Analytics usará o grupo de consumidores padrão. É recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. |
| **Formato de serialização do evento** |O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** | UTF-8 é o único formato de codificação com suporte no momento. |
| **Compactação** (opcional) | O tipo de compactação (Nenhuma, GZip ou Deflate) do fluxo de dados de entrada. |

Quando seus dados forem provenientes de uma fonte de Hub de Eventos, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e a hora em que o evento foi recebido pelos Hubs de eventos. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-data-stream-input-from-iot-hub"></a>Criar entrada de fluxo de dados do Hub IoT
O Hub IoT do Azure é um ingestor de eventos altamente escalonável de publicação/assinatura e otimizado para cenários de IoT.

O carimbo de data/hora padrão de eventos provenientes de um Hub IoT no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Apenas as mensagens enviadas com uma propriedade `DeviceClient` podem ser processadas.
> 
> 

### <a name="consumer-groups"></a>Grupos de consumidores
Cada entrada do trabalho do Hub IoT do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou ele tem várias entradas, algumas entradas podem ser lidas por mais de um leitor de downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite do Hub IoT do Azure dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um Hub IoT como um fluxo de dados de entrada
A tabela a seguir explica cada propriedade na folha **Nova Entrada** no Portal do Azure, quando você configura um Hub IoT como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada.|
| **Hub IoT** |O nome do Hub IoT para usar como entrada. |
| **Ponto de extremidade** |O ponto de extremidade para o hub IoT.|
| **Nome da política de acesso compartilhado** |A política de acesso compartilhado que fornece acesso ao Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| **Chave da política de acesso compartilhado** |A chave de acesso compartilhado usada para autenticar o acesso ao Hub IoT. |
| **Grupo de Consumidores** (opcional) |O grupo de consumidores para ingerir dados do Hub IoT. Se nenhum grupo de consumidores for especificado, um trabalho do Stream Analytics usará o grupo de consumidores padrão. É recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. |
| **Formato de serialização do evento** |O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** |UTF-8 é o único formato de codificação com suporte no momento. |
| **Compactação** (opcional) | O tipo de compactação (Nenhuma, GZip ou Deflate) do fluxo de dados de entrada. |

Quando seus dados forem provenientes de uma fonte de Hub IoT, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** |A data e a hora em que o evento foi recebido pelo Hub IoT. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Uma ID usada para correlacionar a comunicação bidirecional no Hub IoT. |
| **IoTHub.CorrelationId** |Uma ID usada em respostas a mensagens e comentários no Hub IoT. |
| **IoTHub.ConnectionDeviceId** |A ID de autenticação usada para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** |A ID de geração do dispositivo autenticado que foi usado para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.EnqueuedTime** |A hora do recebimento da mensagem pelo Hub IoT. |
| **IoTHub.StreamId** |A propriedade de evento personalizado adicionada pelo dispositivo do remetente. |


## <a name="create-data-stream-input-from-blob-storage"></a>Criar entrada de fluxo de dados do Armazenamento de Blobs
Para cenários com grandes quantidades de dados não estruturados para repositório na nuvem, o Armazenamento de Blobs do Azure oferece uma solução econômica e escalonável. Dados no Armazenamento de Blobs geralmente são considerados dados em repouso. No entanto, ele pode ser processado como um fluxo de dados pelo Stream Analytics. Um cenário típico para entradas de Armazenamento de Blobs com o Stream Analytics é o processamento de log. Nesse cenário, os dados telemétricos foram capturados de um sistema e precisam ser analisados e processados para extrair dados significativos.

O carimbo de data/hora padrão de eventos de Armazenamento de Blobs no Stream Analytics é o carimbo de data/hora que o blob foi modificado pela última vez, que é `BlobLastModifiedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

As entradas formatadas em CSV *exigem* uma linha de cabeçalho para definir os campos do conjunto de dados. Além disso, todos os campos de linha de cabeçalho adicionais devem ser exclusivos.

> [!NOTE]
> O Stream Analytics não dá suporte para a adição de conteúdo a um arquivo de blob existente. O Stream Analytics exibirá cada arquivo apenas uma vez e quaisquer alterações que ocorram no arquivo após o trabalho ter lido os dados não serão processados. A prática recomendada é carregar todos os dados para um arquivo de blob de uma vez e, em seguida, adicionar outros eventos mais recentes em um arquivo diferente, o novo arquivo de blob.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Configurar o Armazenamento de Blobs como um fluxo de dados de entrada

A tabela a seguir explica cada propriedade na folha **Nova entrada** no Portal do Azure, quando você configura o Armazenamento de Blobs como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Conta de armazenamento** | O nome da conta de armazenamento em que estão localizados os arquivos de blob. |
| **Chave de conta de armazenamento** | A chave secreta associada à conta de armazenamento. |
| **Contêiner** | O contêiner para o blob de entrada. Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço de Armazenamento de Blobs do Azure, você deve especificar um contêiner para aquele blob. |
| **Padrão do caminho** (opcional) | O caminho do arquivo usado para localizar os blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das três variáveis a seguir: `{date}`, `{time}` ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O caractere `*` não é um valor permitido para o prefixo de caminho. Apenas <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blobs do Azure</a> válidos são permitidos. |
| **Formato de data** (opcional) | Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se você usar a variável de hora no caminho, o formato de hora no qual os arquivos são organizados. Atualmente, o único valor com suporte é `HH`. |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) para fluxos de dados de entrada. |
| **Codificação** | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| **Compactação** (opcional) | O tipo de compactação (Nenhuma, GZip ou Deflate) do fluxo de dados de entrada. |

Quando seus dados forem provenientes de uma fonte de Armazenamento de Blobs, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada de onde o evento veio. |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e hora da última modificação do blob. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você aprendeu sobre as opções de conexão de dados no Azure para seus trabalhos do Stream Analytics. Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
