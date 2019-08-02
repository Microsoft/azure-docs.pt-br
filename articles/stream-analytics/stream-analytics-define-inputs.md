---
title: Transmitir dados como entrada no Azure Stream Analytics
description: Saiba mais sobre como configurar uma conexão de dados no Azure Stream Analytics. As entradas incluem um fluxo de dados de eventos e também dados de referência.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 1f03f9e68640edd73d2f6bb55cf205a609450658
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620511"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Transmitir dados como entrada no Stream Analytics

O Stream Analytics tem integração de primeira classe com fluxos de dados do Azure como entradas de três tipos de recursos:

- [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Esses recursos de entrada podem residir na mesma assinatura do Azure que o trabalho do Stream Analytics, ou em uma assinatura diferente.

### <a name="compression"></a>Compactação

O Stream Analytics oferece suporte à compactação em todas as fontes de entrada de fluxo de dados. Tipos de compactação com suporte são: nenhum, GZip e compactação Deflate. O suporte para a compactação não está disponível para os dados de referência. Se o formato de entrada forem dados Avro compactados, eles serão manipulados de forma transparente. Você não precisa especificar o tipo de compactação com a serialização Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou testar entradas

Você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), e [Visual Studio Code](quick-create-vs-code.md) para adicionar e exibir ou editar entradas existentes em seu trabalho de streaming. Você também pode testar conexões de entrada e [testar consultas](stream-analytics-manage-job.md#test-your-query) de dados de exemplo do portal do Azure [Visual Studio](stream-analytics-vs-tools-local-run.md), e [Visual Studio Code](vscode-local-run.md). Quando você escreve uma consulta, você pode listar a entrada na cláusula FROM. Você pode obter a lista de entradas disponíveis na página **Consulta** no portal. Se você quiser usar várias entradas, poderá usar `JOIN` para associá-las ou codificar várias consultas `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados dos Hubs de Eventos

Os Hubs de Eventos do Azure fornecem ingestores de eventos altamente escalonável de publicação/assinatura. Um hub de eventos pode coletar milhões de eventos por segundo para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Juntos, os Hubs de Eventos e o Stream Analytics fornecem uma solução de ponta a ponta para análise em tempo real. Os Hubs de Eventos permitem que você envie eventos para o Azure em tempo real, e os trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, você pode enviar cliques da Web, leituras do sensor ou eventos de log online para Hubs de Eventos. Em seguida, você pode criar trabalhos do Stream Analytics para usar Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

`EventEnqueuedUtcTime` é o carimbo de data/hora da chegada de um evento em um hub de eventos e é o carimbo de data/hora padrão de eventos provenientes dos Hubs de Eventos para o Stream Analytics. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores de Hubs de eventos

Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou tem várias entradas, algumas entradas podem ser lidas por mais de um leitor downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics. Também há um limite de 20 grupos de consumidores para um hub de eventos de camada Standard. Para obter mais informações, consulte [entradas Solucionar problemas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada de Hubs de eventos

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure para transmitir entrada de dados de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual existem os recursos de hub de Eventos. | 
| **Namespace do Hub de Eventos** | Um namespace Hub de Eventos é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também cria o namespace. |
| **Nome do Hub de Eventos** | O nome do Hub de Eventos para usar como entrada. |
| **Nome da política do Hub de Eventos** | A política de acesso compartilhado que fornece acesso ao Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub de Eventos manualmente.|
| **Grupo de consumidores de Hub de Eventos** (recomendado) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. Esta cadeia de caracteres identifica o grupo de consumidores a ser usado para ingerir dados do hub de eventos. Se nenhum grupo de consumidores for especificado, o trabalho do Stream Analytics usará o grupo de consumidores $Default.  |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
| **Codificação** | UTF-8 é o único formato de codificação com suporte no momento. |
| **Tipo de compactação do evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |

Quando seus dados forem provenientes de uma entrada de fluxo de Hub de Eventos, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e a hora em que o evento foi recebido pelos Hubs de eventos. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ao usar o Hub de Eventos como um ponto de extremidade das Rotas do Hub IoT, é possível acessar os metadados do Hub IoT usando a [função GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Transmitir dados do Hub IoT

O IoT Hub do Azure é altamente escalonável de publicação-assinatura ingestor de eventos, otimizado para cenários de IoT.

O carimbo de data/hora padrão de eventos provenientes de um Hub IoT no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores do Hub IOT

Cada entrada do Hub IoT do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou ele tem várias entradas, algumas entradas podem ser lidas por mais de um leitor de downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite do Hub IoT do Azure dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um Hub IoT como uma entrada do fluxo de dados

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure, quando você configura um Hub IoT como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada.|
| **Assinatura** | Escolha a assinatura na qual existem os recursos de Hub IoT existentes. | 
| **Hub IoT** | O nome do Hub IoT para usar como entrada. |
| **Ponto de extremidade** | O ponto de extremidade para o Hub IoT.|
| **Nome da política de acesso compartilhado** | A política de acesso compartilhado que fornece acesso ao Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| **Chave da política de acesso compartilhado** | A chave de acesso compartilhado usada para autorizar o acesso ao Hub IoT.  Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub IoT manualmente. |
| **Grupo de consumidores** | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. O grupo de consumidores é usado para ingerir dados do Hub IoT. O Stream Analytics usa o grupo de consumidores $Default, a menos que você especifique o contrário.  |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
| **Codificação** | UTF-8 é o único formato de codificação com suporte no momento. |
| **Tipo de compactação do evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |


Ao usar dados de fluxo provenientes de um Hub IoT, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** | A data e a hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e a hora em que o evento foi recebido pelo Hub IoT. |
| **PartitionId** | A ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Uma ID usada para correlacionar a comunicação bidirecional no Hub IoT. |
| **IoTHub.CorrelationId** | Uma ID usada em respostas a mensagens e comentários no Hub IoT. |
| **IoTHub.ConnectionDeviceId** | A ID de autenticação usada para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** | A ID de geração do dispositivo autenticado que foi usado para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.EnqueuedTime** | A hora do recebimento da mensagem pelo Hub IoT. |


## <a name="stream-data-from-blob-storage"></a>Transmitir dados do armazenamento de blobs
Para cenários com grandes quantidades de dados não estruturados para repositório na nuvem, o Armazenamento de Blobs do Azure oferece uma solução econômica e escalonável. Os dados no Armazenamento de Blobs são geralmente considerados dados em repouso; no entanto, eles podem ser processados como um fluxo de dados pelo Stream Analytics. 

O processamento de log é um cenário bastante usado para o uso de entradas de armazenamento de Blobs com o Stream Analytics. Nesse cenário, os arquivos de dados telemétricos foram capturados de um sistema e precisam ser analisados e processados para extrair dados significativos.

O carimbo de data/hora padrão de eventos de Armazenamento de Blobs no Stream Analytics é o carimbo de data/hora que o blob foi modificado pela última vez, que é `BlobLastModifiedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Um trabalho do Stream Analytics extrai dados de entrada do armazenamento de BLOBs do Azure cada segundo se o arquivo de blob estiver disponível. Se o arquivo de blob não estiver disponível, não há uma retirada exponencial com um atraso de tempo máximo de 90 segundos.

Entradas formatadas em CSV exigem uma linha de cabeçalho para definir os campos de conjunto de dados e todos os campos de linha de cabeçalho devem ser exclusivos.

> [!NOTE]
> O Stream Analytics não dá suporte para a adição de conteúdo a um arquivo de blob existente. O Stream Analytics exibirá cada arquivo apenas uma vez e quaisquer alterações que ocorram no arquivo após o trabalho ter lido os dados não serão processados. A prática recomendada é carregar todos os dados para um arquivo de blob de uma vez e, em seguida, adicionar outros eventos mais recentes em um arquivo diferente, o novo arquivo de blob.

Carregar um número muito grande de blobs ao mesmo tempo pode causar o Stream Analytics ignorar a leitura alguns blobs em casos raros. É recomendável para carregar blobs pelo menos 2 segundos de diferença no armazenamento de BLOBs. Se essa opção não for viável, você pode usar os Hubs de eventos para grandes volumes de fluxo de eventos. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o Armazenamento de Blobs como uma entrada de dados 

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure, quando você configura o Armazenamento de Blobs como uma entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual existem os recursos de Hub IoT existentes. | 
| **Conta de armazenamento** | O nome da conta de armazenamento em que estão localizados os arquivos de blob. |
| **Chave de conta de armazenamento** | A chave secreta associada à conta de armazenamento. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Armazenamento de Blobs manualmente. |
| **Contêiner** | O contêiner para o blob de entrada. Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço de Armazenamento de Blobs do Azure, você deve especificar um contêiner para aquele blob. Você pode escolher **Usar contêiner existente** ou **Criar novo** para ter um novo contêiner criado.|
| **Padrão do caminho** (opcional) | O caminho do arquivo usado para localizar os blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das três variáveis a seguir: `{date}`, `{time}` ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O caractere `*` não é um valor permitido para o prefixo de caminho. Apenas <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blobs do Azure</a> válidos são permitidos. Não inclua nomes de contêiner ou de arquivo. |
| **Formato de data** (opcional) | Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Atualmente, o único valor com suporte é `HH` para horas. |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
| **Codificação** | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| **Compactação** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |

Quando seus dados forem provenientes de uma fonte de Armazenamento de Blobs, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada de onde o evento veio. |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e hora da última modificação do blob. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
