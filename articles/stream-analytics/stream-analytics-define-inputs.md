---
title: Transmitir dados como entrada no Azure Stream Analytics
description: Saiba mais sobre como configurar uma conexão de dados no Azure Stream Analytics. As entradas incluem um fluxo de dados de eventos e também dados de referência.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 5f10fed66475cda8fd700a4737727101e2465870
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019356"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Transmitir dados como entrada no Stream Analytics

O Stream Analytics tem integração de primeira classe com fluxos de dados do Azure como entradas de três tipos de recursos:

- [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Esses recursos de entrada podem residir na mesma assinatura do Azure que o trabalho do Stream Analytics, ou em uma assinatura diferente.

### <a name="compression"></a>Compactação

O Stream Analytics oferece suporte à compactação em todas as fontes de entrada de fluxo de dados. Os tipos de compactação compatíveis são: nenhum, GZip e compactação Deflate. O suporte para a compactação não está disponível para os dados de referência. Se o formato de entrada forem dados Avro compactados, eles serão manipulados de forma transparente. Você não precisa especificar o tipo de compactação com a serialização Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou testar entradas

Você pode usar o [Portal do Azure](stream-analytics-quick-create-portal.md), o [Visual Studio](stream-analytics-quick-create-vs.md) e o [Visual Studio Code](quick-create-visual-studio-code.md) para adicionar e exibir ou editar entradas existentes no trabalho de streaming. Além disso, você pode testar conexões de entrada e consultas de teste de dados de exemplo no portal do Azure, no [Visual Studio](stream-analytics-vs-tools-local-run.md) e no [Visual Studio Code](visual-studio-code-local-run.md). Ao gravar uma consulta, você lista a entrada na cláusula FROM. Você pode obter a lista de entradas disponíveis na página **Consulta** no portal. Se você quiser usar várias entradas, poderá usar `JOIN` para associá-las ou codificar várias consultas `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados dos Hubs de Eventos

Os hubs de eventos do Azure fornecem investidores de eventos de publicação/assinatura altamente escalonáveis. Um Hub de Eventos pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Juntos, os Hubs de Eventos e o Stream Analytics fornecem uma solução de ponta a ponta para análise em tempo real. Os Hubs de Eventos permitem que você envie eventos para o Azure em tempo real, e os trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, você pode enviar cliques da Web, leituras do sensor ou eventos de log online para Hubs de Eventos. Em seguida, você pode criar trabalhos do Stream Analytics para usar Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

`EventEnqueuedUtcTime` é o carimbo de data/hora da chegada de um evento em um hub de eventos e é o carimbo de data/hora padrão de eventos provenientes dos Hubs de Eventos para o Stream Analytics. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="event-hubs-consumer-groups"></a>Grupo de consumidores de Hubs de Eventos

Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou tem várias entradas, algumas entradas podem ser lidas por mais de um leitor downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics. Também há um limite de 20 grupos de consumidores por Hub de Eventos de nível padrão. Para obter mais informações, consulte [entradas Solucionar problemas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada dos Hubs de Eventos

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure para transmitir entrada de dados de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual existem os recursos de hub de Eventos. | 
| **Namespace do Hub de Eventos** | Um namespace Hub de Eventos é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também cria o namespace. |
| **Nome do Hub de Eventos** | O nome do Hub de Eventos para usar como entrada. |
| **Nome da política do Hub de Eventos** | A política de acesso compartilhado que fornece acesso ao Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub de Eventos manualmente.|
| **Grupo de consumidores de Hub de Eventos** (recomendado) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. Esta cadeia de caracteres identifica o grupo de consumidores a ser usado para ingerir dados do hub de eventos. Se nenhum grupo de consumidores for especificado, o trabalho do Stream Analytics usará o grupo de consumidores $Default.  |
| **Chave de partição** | Este é um campo opcional disponível somente se o trabalho estiver configurado para usar o [nível de compatibilidade](./stream-analytics-compatibility-level.md) 1,2 ou superior. Se a entrada for particionada por uma propriedade, você poderá adicionar o nome dessa propriedade aqui. Isso é usado para melhorar o desempenho da consulta se ela incluir uma cláusula PARTITION BY ou GROUP BY nessa propriedade. Se esse trabalho usa o nível de compatibilidade 1,2 ou superior, esse campo assume como padrão "PartitionID". |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV, Avro ou [outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
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
> Ao usar o Hub de Eventos como um ponto de extremidade das Rotas do Hub IoT, é possível acessar os metadados do Hub IoT usando a [função GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Transmitir dados do Hub IoT

O Hub IoT do Azure é um ingestor de eventos altamente escalonável de publicação/assinatura e otimizado para cenários de IoT.

O carimbo de data/hora padrão de eventos provenientes de um Hub IoT no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="iot-hub-consumer-groups"></a>Grupo de consumidores do Hub IoT

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
| **Chave de partição** | Este é um campo opcional disponível somente se o trabalho estiver configurado para usar o [nível de compatibilidade](./stream-analytics-compatibility-level.md) 1,2 ou superior. Se a entrada for particionada por uma propriedade, você poderá adicionar o nome dessa propriedade aqui. Isso é usado para melhorar o desempenho da consulta se ela incluir uma cláusula PARTITION BY ou GROUP BY nessa propriedade. Se esse trabalho usa o nível de compatibilidade 1,2 ou superior, esse campo assume como padrão "PartitionID". |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV, Avro ou [outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
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


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>Transmitir dados do armazenamento de BLOBs ou Data Lake Storage Gen2
Para cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento de BLOBs do Azure ou o Azure Data Lake Storage Gen2 (ADLS Gen2) oferece uma solução econômica e escalonável. Os dados no armazenamento de BLOBs ou ADLS Gen2 geralmente são considerados dados em repouso; no entanto, esses dados podem ser processados como um fluxo de dados por Stream Analytics. 

O processamento de log é um cenário comumente usado para usar essas entradas com Stream Analytics. Nesse cenário, os arquivos de dados telemétricos foram capturados de um sistema e precisam ser analisados e processados para extrair dados significativos.

O carimbo de data/hora padrão de um armazenamento de BLOBs ou ADLS Gen2 evento em Stream Analytics é o carimbo de data/hora em que foi modificado pela última vez, que é `BlobLastModifiedUtcTime` . Se um blob for carregado em uma conta de armazenamento em 13:00 e o trabalho de Azure Stream Analytics for iniciado usando a opção *agora* às 13:01, ele não será selecionado, pois sua hora modificada fica fora do período de execução do trabalho.

Se um blob for carregado em um contêiner da conta de armazenamento às 13:00 e o trabalho do Azure Stream Analytics for iniciado usando a opção *Agora* às 13:00 ou antes, o blob não será selecionado, pois sua hora de modificação fica fora do período de execução do trabalho.

Se um trabalho do Azure Stream Analytics for iniciado usando *Agora* às 13:00 e um blob for carregado para o contêiner da conta de armazenamento às 13:01, o Azure Stream Analytics selecionará o blob. O carimbo de data/hora atribuído a cada blob é baseado apenas no `BlobLastModifiedTime`. A pasta em que o blob está não tem nenhuma relação com o carimbo de data/hora atribuído. Por exemplo, se houver um blob *2019/10-01/00/b1. txt* com um `BlobLastModifiedTime` de 2019-11-11, o carimbo de data/hora atribuído a esse blob será 2019-11-11.

Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](/stream-analytics-query/stream-analytics-query-language-reference). Um trabalho Stream Analytics efetua pull de dados do armazenamento de BLOBs do Azure ou ADLS Gen2 entrada a cada segundo se o arquivo de blob estiver disponível. Se o arquivo de blob não estiver disponível, não há uma retirada exponencial com um atraso de tempo máximo de 90 segundos.

As entradas formatadas em CSV exigem uma linha de cabeçalho para definir os campos do conjunto de dados, e todos os campos de linha de cabeçalho devem ser exclusivos.

> [!NOTE]
> O Stream Analytics não dá suporte para a adição de conteúdo a um arquivo de blob existente. O Stream Analytics exibirá cada arquivo apenas uma vez e quaisquer alterações que ocorram no arquivo após o trabalho ter lido os dados não serão processados. A prática recomendada é carregar todos os dados para um arquivo de blob de uma vez e, em seguida, adicionar outros eventos mais recentes em um arquivo diferente, o novo arquivo de blob.

Em cenários em que muitos BLOBs são adicionados continuamente e Stream Analytics está processando os BLOBs à medida que eles são adicionados, é possível que alguns BLOBs sejam ignorados em casos raros devido à granularidade do `BlobLastModifiedTime` . Você pode mitigar isso carregando blobs com pelo menos dois segundos de diferença. Se essa opção não for viável, use os Hubs de Eventos para transmitir grandes volumes de eventos.

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o Armazenamento de Blobs como uma entrada de dados 

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure, quando você configura o Armazenamento de Blobs como uma entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual o recurso de armazenamento existe. | 
| **Conta de armazenamento** | O nome da conta de armazenamento em que estão localizados os arquivos de blob. |
| **Chave de conta de armazenamento** | A chave secreta associada à conta de armazenamento. Essa opção é preenchida automaticamente no, a menos que você selecione a opção para fornecer as configurações manualmente. |
| **Contêiner** | Os contêineres fornecem um agrupamento lógico para BLOBs. Você pode escolher **Usar contêiner existente** ou **Criar novo** para ter um novo contêiner criado.|
| **Padrão do caminho** (opcional) | O caminho do arquivo usado para localizar os blobs no contêiner especificado. Se você quiser ler os blobs da raiz do contêiner, não defina um padrão de caminho. No caminho, você pode optar por especificar uma ou mais instâncias das três variáveis a seguir: `{date}`, `{time}` ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O caractere `*` não é um valor permitido para o prefixo de caminho. Apenas <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">caracteres de blobs do Azure</a> válidos são permitidos. Não inclua nomes de contêiner ou de arquivo. |
| **Formato de data** (opcional) | Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Exemplo: `YYYY/MM/DD` <br/><br/> Quando a entrada do blob tem `{date}` ou `{time}` no caminho, as pastas são examinadas em ordem de tempo crescente.|
| **Formato de hora** (opcional) |  Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Atualmente, o único valor com suporte é `HH` para horas. |
| **Chave de partição** | Este é um campo opcional disponível somente se o trabalho estiver configurado para usar o [nível de compatibilidade](./stream-analytics-compatibility-level.md) 1,2 ou superior. Se a entrada for particionada por uma propriedade, você poderá adicionar o nome dessa propriedade aqui. Isso é usado para melhorar o desempenho da consulta se ela incluir uma cláusula PARTITION BY ou GROUP BY nessa propriedade. Se esse trabalho usa o nível de compatibilidade 1,2 ou superior, esse campo assume como padrão "PartitionID". |
| **Contagem de partições de entrada** | Este campo está presente somente quando {Partition} está presente no padrão de caminho. O valor dessa propriedade é um inteiro >= 1. Sempre que {Partition} aparecer em pathPattern, um número entre 0 e o valor desse campo-1 será usado. |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV, Avro ou [outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados de entrada.  Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais. |
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
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/