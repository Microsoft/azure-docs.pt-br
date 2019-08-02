---
title: Saída do Azure Stream Analytics para Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para salvar a saída no Azure Cosmos DB para saída JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: de5febaeecd176a8718364720132d3fa4433c57f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443622"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics para Azure Cosmos DB  
O Stream Analytics pode direcionar o [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para uma saída em JSON, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, consulte [Roteiro de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução. 

> [!Note]
> Neste momento, o Azure Stream Analytics oferece suporte apenas à conexão ao Microsoft Azure CosmosDB usando a **API do SQL**.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Conceitos básicos do Cosmos DB como um destino de saída
A saída do Azure Cosmos DB no Stream Analytics permite a gravação seu fluxo de processamento de resultados como saída JSON em seu contêiner do Cosmos DB (s). Stream Analytics não criar contêineres no seu banco de dados, em vez disso, exigindo que você criá-los com antecedência. Isso é para que os custos de cobrança de contêineres do Cosmos DB são controlados por você, e para que você possa ajustar o desempenho, a consistência e a capacidade de seus contêineres diretamente usando o [APIs do Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos no firewall do seu Microsoft Azure Cosmos DB.

Algumas das opções de contêiner do Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, a disponibilidade e a latência
Para corresponder aos seus requisitos de aplicativo, o Azure Cosmos DB permite que você ajuste o banco de dados e os contêineres e fazer compensações entre consistência, disponibilidade, latência e taxa de transferência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Taxa de transferência pode ser melhorada ao escalar verticalmente Units(RUs) solicitar no contêiner. Também por padrão, Azure Cosmos DB permite a indexação síncrona em cada operação CRUD para seu contêiner. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Microsoft Azure Cosmos DB. Para obter mais informações, consulte o artigo [alterar o banco de dados e níveis de consistência de consulta](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
A integração do Stream Analytics com o Azure Cosmos DB permite que você inserir ou atualizar registros em seu contêiner com base em uma determinada coluna de ID do documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem upsert otimista, na qual as atualizações são feitas somente quando a inserção falha com um conflito de ID do Documento. Com 1.0 de nível de compatibilidade, essa atualização é executada como um PATCH, portanto, ele permite atualizações parciais para o documento, ou seja, a adição de novas propriedades ou substituindo que uma propriedade existente é realizada incrementalmente. Porém, as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada. Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento. Isso é descrito mais detalhadamente na seção 1.2 de nível de compatibilidade abaixo.

Se o documento JSON recebido tiver um campo de ID existente, esse campo será usado automaticamente como coluna de ID do documento do Cosmos DB e quaisquer gravações subsequentes serão tratadas como tal, levando a uma destas situações:
- IDs exclusivas levam a inserção
- IDs duplicadas e 'ID do documento' definida como 'ID' levam a upsert
- IDs duplicadas e 'ID do documento' não definida leva a erros após o primeiro documento

Se você quiser salvar <i>todos</i> os documentos, incluindo aqueles com uma ID duplicada, renomeie o campo de ID em sua consulta (com a palavra-chave AS) e permita que o Cosmos DB crie o campo de ID ou substitua a ID por outro valor de coluna (usando a palavra-chave AS ou usando a configuração de ‘ID do documento’).

## <a name="data-partitioning-in-cosmos-db"></a>Particionamento de dados no Cosmos DB
Os contêineres [ilimitados](../cosmos-db/partition-data.md) do Azure Cosmos DB são a abordagem recomendada para particionar seus dados, à medida que o Azure Cosmos DB automaticamente dimensiona partições com base em sua carga de trabalho. Ao gravar os contêineres ilimitados, o Stream Analytics usa tantos gravadores paralelos como a etapa de consulta anterior ou entrada de esquema de particionamento.
> [!NOTE]
> Neste momento, o Azure Stream Analytics suporta apenas os contêineres ilimitados com chaves de partição no nível superior. Por exemplo, `/region` tem suporte. Não há suporte para chaves de partição aninhadas (por exemplo, `/region/name`). 

Dependendo de sua escolha de chave de partição, você poderá receber esse _aviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade de chave de partição que tem um número de valores distintos e permite que você distribua a carga de trabalho uniformemente entre esses valores. Como um artefato natural de particionamento, solicitações que envolvem a mesma chave de partição são limitadas por taxa de transferência máxima de uma única partição. Além disso, o tamanho do armazenamento para documentos pertencentes a mesma chave de partição é limitado a 10GB. Uma chave de partição ideal é aquela que é exibida com frequência, como um filtro nas suas consultas, e tem cardinalidade suficiente para garantir que sua solução seja escalonável.

Uma chave de partição também é o limite para transações em procedimentos armazenados e gatilhos do DocumentDB. Você deve escolher a chave de partição para que os documentos que ocorrem juntos em transações compartilham o mesmo valor de chave de partição. O artigo [particionamento no Cosmos DB](../cosmos-db/partitioning-overview.md) fornece mais detalhes sobre como escolher uma chave de partição.

Para contêineres do Azure Cosmos DB fixos, o Stream Analytics permite que nenhuma maneira de escalar vertical ou horizontalmente, depois que estão cheias. Eles têm um limite de 10 GB e taxa de transferência de 10.000 RU/s.  Para migrar dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), você precisa usar a [ferramenta de migração de dados](../cosmos-db/import-data.md) ou a [biblioteca de feed de alterações](../cosmos-db/change-feed.md).

A capacidade de gravar em vários contêineres fixos está sendo preterida e não é recomendada para escalar horizontalmente seu trabalho do Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Taxa de transferência aprimorada com compatibilidade nível 1.2
Com nível de compatibilidade 1.2, Stream Analytics oferece suporte à integração nativa em massa de gravação no Cosmos DB. Isso permite a gravação efetivamente ao Cosmos DB com a maximizar a taxa de transferência e com eficiência as solicitações de limitação do identificador. O mecanismo de gravação aprimorado está disponível em um novo nível de compatibilidade devido a uma diferença de comportamento de upsert.  Antes de 1.2, o comportamento de upsert é inserir ou mesclar o documento. Com 1.2, o comportamento de upserts é modificado para inserir ou substituir o documento. 

Antes de 1,2, usa um procedimento armazenado personalizado para documentos de upsert em massa por chave de partição para o Cosmos DB, em que um lote é gravado como uma transação. Mesmo quando um único registro atinge um erro transitório (limitação), o lote inteiro deve ser repetido. Isso tornou cenários com limitação mesmo razoável relativamente mais lenta. Comparação a seguir mostra como esses trabalhos, iria se comportar com 1.2.

O exemplo a seguir mostra dois trabalhos do Stream Analytics idênticos lendo a mesma entrada de Hub de eventos. Ambos os trabalhos do Stream Analytics estão [totalmente particionada](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e a gravação para contêineres do cosmos DB idênticos. Métricas à esquerda são de trabalho configurado com o nível de compatibilidade 1.0 e aqueles à direita são configurados com 1.2. Chave de partição de um contêiner Cosmos DB é um GUID exclusivo provenientes do evento de entrada.

![comparação de métricas de análise de fluxo](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Taxa de eventos de entrada no Hub de eventos é 2 vezes maior do que os contêineres do Cosmos DB (20 mil RUs) estão configurados para suportar, portanto, a limitação é esperada no Cosmos DB. No entanto, o trabalho com 1.2, consistentemente está gravando em uma maior taxa de transferência (eventos de saída/minuto) e com uma menor utilização média de % SU. Em seu ambiente, essa diferença dependerá poucos mais fatores como a opção de formato de evento, o tamanho de mensagem/evento de entrada, as chaves de partição, consulta etc.

![comparação de métricas do cosmos db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com 1.2, o Stream Analytics é mais inteligente no utilizando 100% do que a taxa de transferência disponível no Cosmos DB com muito poucas reenvios de limitação de taxa de limitação /. Isso fornece uma experiência melhor para outras cargas de trabalho, como consultas em execução no contêiner ao mesmo tempo. Caso você precise experimentar como ASA pode ser dimensionado com o Cosmos DB como um coletor para mil de 1 a 10 mil mensagens por segundo, aqui está uma [projeto de exemplos do azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) que permite fazer isso.
Observe que a taxa de transferência de saída do Cosmos DB é idêntica com 1.0 e 1.1. Como 1.2 não é atualmente o padrão, você pode [definir nível de compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) para um trabalho de Stream Analytics por meio do portal ou usando o [criar a chamada à API REST trabalho](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Ele tem *altamente recomendável* usar 1.2 de nível de compatibilidade no ASA com o Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Configurações do Cosmos DB para saída em JSON

A criação do Cosmos DB como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostrado abaixo. Esta seção fornece uma explicação da definição de propriedades.

![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | DESCRIÇÃO|
|-------------   | -------------|
|Alias de saída    | Um alias para se referir a essa saída em sua consulta do ASA.|
|Assinatura    | Escolha a assinatura do Azure.|
|ID da Conta      | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB.|
|Chave de conta     | A chave de acesso compartilhado da conta do Azure Cosmos DB.|
|Banco de dados        | O nome do banco de dados do Azure Cosmos DB.|
|Nome do contêiner | O nome do contêiner a ser usado. `MyContainer` é uma entrada válida do exemplo – um contêiner denominado `MyContainer` deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída usado como a chave exclusiva que inserir ou atualizar as operações que devem ser baseadas. Se deixado vazio, todos os eventos serão inseridos, com nenhuma opção de atualização.|
