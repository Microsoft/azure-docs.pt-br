---
title: Etapas de pré-migração para a migração de dados para a API do Azure Cosmos DB para o MongoDB
description: Este documento fornece uma visão geral dos pré-requisitos para uma migração de dados do MongoDB para o Azure Cosmos DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: chrande
ms.openlocfilehash: 337341daf0e092def639a4e8f6fc8ee0a9b57c75
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349411"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para a migração de dados do MongoDB para a API do Azure Cosmos DB para o MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Antes de migrar seus dados do MongoDB (local ou na nuvem) para a API do Azure Cosmos DB para o MongoDB, você deve:

1. [Leia as principais considerações sobre como usar a API do Azure Cosmos DB para o MongoDB](#considerations)
2. [Escolher uma opção para a migração dos dados](#options)
3. [Estimar a taxa de transferência necessária para as suas cargas de trabalho](#estimate-throughput)
4. [Escolher a chave de partição ideal para seus dados](#partitioning)
5. [Entender a política de indexação que você pode definir nos seus dados](#indexing)

Se você já tiver concluído os pré-requisitos acima para a migração, poderá [Migrar dados do MongoDB para a API do Azure Cosmos DB para o MongoDB usando o 	Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db.md). Além disso, se você não tiver criado uma conta, poderá procurar qualquer um dos [Guias de Início Rápido](create-mongodb-dotnet.md) que mostrem as etapas para a criação de uma conta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerações sobre o uso da API do Azure Cosmos DB para o MongoDB

Veja a seguir características específicas sobre a API do Azure Cosmos DB para o MongoDB:

- **Modelo de capacidade**: A capacidade do banco de dados no Azure Cosmos DB baseia-se em um modelo baseado em taxa de transferência. Esse modelo baseia-se em [Unidades de Solicitação por segundo](request-units.md), que é uma unidade que representa o número de operações de banco de dados que podem ser executadas em uma coleção por segundo. Essa capacidade pode ser alocada no [nível de um banco de dados ou coleção](set-throughput.md) e pode ser provisionada em um modelo de alocação ou usando a [taxa de transferência provisionada de dimensionamento automático](provision-throughput-autoscale.md).

- **Unidades de Solicitação**: Cada operação de banco de dados tem um custo de RUs (unidades de solicitação) associado no Azure Cosmos DB. Quando executado, isso é subtraído do nível de unidades de solicitação disponíveis em determinado segundo. Se uma solicitação exigir mais RUs do que as RUs alocadas no momento, há duas opções para resolver o problema: aumentar a quantidade de RUs ou aguardar até que o próximo segundo inicie e repetir a operação.

- **Capacidade elástica**: A capacidade de uma determinada coleção ou banco de dados pode ser alterada a qualquer momento. Isso permite que o banco de dados se adapte de forma elástica aos requisitos de taxa de transferência da sua carga de trabalho.

- **Fragmentação automática**: O Azure Cosmos DB fornece um sistema de particionamento automático que requer somente um fragmento (ou uma chave de partição). O [mecanismo de particionamento automático](partitioning-overview.md) é compartilhado entre todas as APIs do Azure Cosmos DB e permite dimensionamento de taxa de transferência e dados contínuos através da distribuição horizontal.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opções de migração da API do Azure Cosmos DB para o MongoDB

O [Serviço de Migração de Banco de Dados do Azure para a API do Azure Cosmos DB para o MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornece um mecanismo que simplifica a migração de dados por meio do fornecimento de uma plataforma de hospedagem totalmente gerenciada, opções de monitoramento de migração e manipulação de limitação automática. A lista completa de opções é a seguinte:

|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Online|[Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados e cuida da replicação de alterações dinâmicas <br/>&bull; Funciona somente com outras fontes do MongoDB|
|Offline|[Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados e cuida da replicação de alterações dinâmicas <br/>&bull; Funciona somente com outras fontes do MongoDB|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Fácil de configurar e suporta várias fontes <br/>&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequada para grandes conjuntos de dados <br/>&bull; A falta de pontos de verificação significa que qualquer problema durante o curso da migração exigiria uma reinicialização de todo o processo de migração<br/>&bull; A falta de uma fila de mensagens mortas significa que alguns arquivos errados poderiam parar todo o processo de migração <br/>&bull; Precisa de código personalizado para aumentar a taxa de transferência de leitura para determinadas fontes de dados|
|Offline|[Ferramentas do Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Fácil de configurar e de integrar <br/>&bull; Precisa de manipulação personalizada para limitações|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Estimar a taxa de transferência necessária para as suas cargas de trabalho

No Azure Cosmos DB, a taxa de transferência é provisionada antecipadamente e medida em Unidades de Solicitação (RUs) por segundo. Diferentemente das VMs ou servidores locais, as RUs são fáceis de escalar e reduzir verticalmente a qualquer momento. É possível alterar o número de RUs provisionadas instantaneamente. Para obter mais informações, confira [Unidades de solicitação no Azure Cosmos DB](request-units.md).

Você pode usar a [Calculadora de capacidade do Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para determinar a quantidade de Unidades de Solicitação com base na configuração da sua conta de banco de dados, na quantidade de dados, no tamanho do documento e nas leituras e gravações necessárias por segundo.

Veja a seguir os principais fatores que afetam o número de RUs necessárias:
- **Tamanho do documento**: À medida que o tamanho de um item/documento aumentar, o número de RUs consumidas para leitura ou gravação do item/documento também aumentará.

- **Contagem de propriedades do documento**: o número de RUs consumidas para criar ou atualizar um documento está relacionado ao número, à complexidade e ao comprimento de suas propriedades. Você pode reduzir o consumo de unidade de solicitação para operações de gravação, [limitando o número de propriedades indexadas](mongodb-indexing.md).

- **Padrões de consulta**: A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas por uma consulta. 

A melhor maneira de entender o custo das consultas é usar dados de exemplo no Azure Cosmos DB [e executar consultas de exemplo do MongoDB Shell](connect-mongodb-account.md) usando o comando `getLastRequestStastistics` para obter o encargo da solicitação, o que produzirá o número de RUs consumidas:

`db.runCommand({getLastRequestStatistics: 1})`

Esse comando produzirá um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Você também pode usar [as configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) para entender a frequência e os padrões das consultas executadas em relação ao Azure Cosmos DB. Os resultados dos logs de diagnóstico podem ser enviados para uma conta de armazenamento, uma instância do EventHub ou ao [Log Analytics do Azure](../azure-monitor/log-query/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Escolha sua chave de partição
O particionamento, também conhecido como fragmentação, é um ponto-chave de consideração antes da migração de dados. O Azure Cosmos DB usa o particionamento totalmente gerenciado para aumentar a capacidade em um banco de dados para atender aos requisitos de armazenamento e de taxa de transferência. Esse recurso não precisa da hospedagem nem da configuração de servidores de roteamento.   

De maneira parecida, o recurso de particionamento adiciona a capacidade automaticamente e reequilibra os dados de forma adequada. Para obter detalhes e recomendações sobre como escolher a chave de partição correta para seus dados, consulte o [artigo Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexar os dados

A API do Azure Cosmos DB para o servidor MongoDB versão 3,6 indexa automaticamente o `_id` campo somente. Este campo não pode ser removido. Ele impõe automaticamente a exclusividade do `_id` campo por chave de fragmentação. Para indexar campos adicionais, você aplica os comandos de gerenciamento de índice do MongoDB. Essa política de indexação padrão difere da API de SQL do Azure Cosmos DB, que indexa todos os campos por padrão.

Os recursos de indexação fornecidos pelo Azure Cosmos DB incluem a adição de índices compostos, índices exclusivos e índices TTL (vida útil). A interface de gerenciamento de índice é mapeada para o comando `createIndex()`. Saiba mais na [indexação no artigo API do Azure Cosmos DB para MongoDB](mongodb-indexing.md).

[O Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db.md) migra coleções do MongoDB automaticamente com índices exclusivos. Porém, os índices exclusivos devem ser criados antes da migração. O Azure Cosmos DB não oferece suporte à criação de índices exclusivos, quando já existem dados em suas coleções. Para obter mais informações, veja [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Próximas etapas
* [Migre seus dados do MongoDB para o Azure Cosmos DB usando o Serviço de Migração de Banco de Dados do Azure.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos](set-throughput.md)
* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [A distribuição global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)