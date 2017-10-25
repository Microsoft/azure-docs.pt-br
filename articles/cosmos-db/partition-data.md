---
title: Particionamento e escala horizontal no BD Cosmos do Azure | Microsoft Docs
description: "Saiba mais sobre como o particionamento funciona no DB Cosmos do Azure, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7f5e2939ed09c0fbb4eb81f066075553376ff57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partição e escala no Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de banco de dados globalmente distribuído, multi-modelo projetado para ajudá-lo a atingir um desempenho rápido e previsível. Ele pode ser dimensionado perfeitamente junto com o seu aplicativo à medida que cresce. Este artigo fornece uma visão geral de como modelos de particionamento funcionam para todos os modelos de dados no Azure Cosmos DB. Ele também descreve como você pode configurar contêineres do Azure Cosmos DB contêineres para dimensionar com eficiência seus aplicativos.

Particionamento e chaves de partição são discutidas neste vídeo de sexta-feira do Azure com Scott Hanselman e o Gerente-Chefe de Engenharia do Azure Cosmos DB, Shireesh Thota:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure
No BD Cosmos do Azure, você pode armazenar e consultar dados sem esquema com tempos de resposta da ordem de milissegundos em qualquer escala. O Azure Cosmos DB fornece contêineres para armazenamento de dados denominados *coleções* (para documentos) *gráficos*, ou *tabelas*. Contêineres são recursos lógicos e podem abranger um ou mais servidores ou partições físicas. O número de partições é determinado pelo Azure Cosmos DB com base no tamanho do armazenamento e na taxa de transferência provisionada do contêiner. Cada partição no Azure Cosmos DB tem uma quantidade fixa de armazenamento com suporte de SSD associado a ela e é replicada para alta disponibilidade. O gerenciamento de partição é totalmente gerenciado pelo Azure Cosmos DB e você não precisa escrever um código complexo ou gerenciar suas partições. Os contêineres do Azure Cosmos DB são ilimitados em termos de armazenamento e taxa de transferência. 

![Particionamento de recursos](./media/introduction/azure-cosmos-db-partitioning.png) 

O particionamento é transparente para seu aplicativo. O Azure Cosmos DB dá suporte a leituras e gravações rápidas, consultas, lógica transacional, níveis de consistência e controle de acesso refinado por meio de métodos/APIs para um recurso de contêiner único. O serviço lida com a distribuição de dados em partições e com o roteamento de solicitações de consulta para a partição correta. 

Como o particionamento funciona? Cada item deve ter uma chave de partição e uma chave de linha, que o identificam exclusivamente. Sua chave de partição atua como uma partição lógica para seus dados e fornece ao Azure Cosmos DB um limite natural para a distribuição de dados entre partições. De forma resumida, veja como o particionamento funciona no Azure Cosmos DB:

* Você pode provisionar um contêiner do Azure Cosmos DB com uma taxa de transferência de `T` solicitações/s.
* Nos bastidores, o Azure Cosmos DB provisiona as partições necessárias para atender `T` solicitações/s. Se `T` for maior que a taxa de transferência máxima por partição `t`, o Azure Cosmos DB provisionará `N` = `T/t` partições.
* O Azure Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as `N` partições. Sendo assim, cada partição (partição física) hospeda 1-N valores de chave de partição (partições lógicas).
* Quando uma partição física `p` atingir seu limite de armazenamento, o Azure Cosmos DB divide perfeitamente `p` em duas novas partições, `p1` e `p2`. Ele distribui valores correspondentes para cerca de metade das chaves para cada uma das partições. Essa operação de divisão é invisível para o aplicativo.
* Da mesma forma, quando você provisiona uma taxa de transferência superior à `t*N`, o Azure Cosmos DB divide uma ou mais de suas partições para dar suporte à taxa de transferência mais alta.

A semântica das chaves de partição é ligeiramente diferente para corresponder à semântica de cada API, conforme mostrado na tabela a seguir:

| API | Chave de partição | Chave de linha |
| --- | --- | --- |
| Azure Cosmos DB | Caminho da chave de partição personalizada | `id` fixo | 
| MongoDB | Chave compartilhada personalizada  | `_id` fixo | 
| Gráfico | Propriedade da chave de partição personalizada | `id` fixo | 
| Tabela | `PartitionKey` fixo | `RowKey` fixo | 

O Azure Cosmos DB usa o particionamento baseado em hash. Quando você grava um item, o Azure Cosmos DB faz o hash do valor da chave de partição e usa o resultado com hash para determinar em qual partição deve armazenar o item. O Azure Cosmos DB armazena todos os itens com a mesma chave de partição na mesma partição física. A escolha da chave de partição é uma decisão importante que você precisará fazer no momento do design. Você deve escolher um nome de propriedade que tenha uma ampla gama de valores e tenha padrões de acesso uniformes.

> [!NOTE]
> É uma prática recomendada ter uma chave de partição com muitos valores distintos (centenas a milhares, no mínimo).
>

Os contêineres do Azure Cosmos DB podem ser criados como *fixos* ou *ilimitados*. Contêineres de tamanho fixo têm um limite máximo de 10 GB e taxa de transferência de 10.000 RU/s. Algumas APIs permitem que a chave de partição seja omitida para contêineres de tamanho fixo. Para criar um contêiner ilimitado, você deve especificar uma taxa de transferência mínima de 2.500 RU/s.

É uma boa ideia verificar como os seus dados estão distribuídos nas partições. Para verificar isso no portal, acesse sua conta do Azure Cosmos DB e clique em **Métricas** na seção **Monitoramento** e, em seguida, no painel direito, clique na guia **Armazenamento** para ver como os seus dados estão particionados na diferentes partições físicas.

![Particionamento de recursos](./media/partition-data/partitionkey-example.png)

A imagem à esquerda mostra o resultado de uma chave de partição mal feita e a imagem à direita mostra o resultado de uma chave de partição bem realizada. Na imagem à esquerda, você pode ver que os dados não estão distribuídos uniformemente entre as partições. Você deve se esforçar para distribuir os seus dados de modo que o gráfico fique semelhante à imagem à direita.

## <a name="partitioning-and-provisioned-throughput"></a>Particionamento e produtividade provisionada
O Azure Cosmos DB foi projetado para ter um desempenho previsível. Ao criar um contêiner, você reserva a produtividade em termos de *[RUs](request-units.md) (unidades de solicitação) por segundo*. A cada solicitação é atribuído um custo de RU, que é proporcional à quantidade de recursos do sistema, como CPU, memória e E/S consumida pela operação. Uma leitura de um documento de 1 KB com consistência de sessão consome 1 RU. Uma leitura é 1 RU, independentemente do número de itens armazenados ou do número de solicitações simultâneas em execução ao mesmo tempo. Itens maiores exigem mais RUs, dependendo do tamanho. Se você souber o tamanho de suas entidades e o número de leituras de que precisa para dar suporte para o seu aplicativo, poderá provisionar a quantidade exata produtividade necessária para as necessidades de leitura do seu aplicativo. 

> [!NOTE]
> Para atingir a taxa de transferência total do contêiner, você deve escolher uma chave de partição que permita distribuir uniformemente as solicitações entre alguns valores diferentes de chave de partição.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Trabalhando com APIs do Azure Cosmos DB
Você pode usar o portal do Azure ou a CLI do Azure para criar contêineres e dimensioná-los a qualquer momento. Esta seção mostra como criar contêineres e especificar a definição de chave de partição e de taxa de transferência em cada uma das APIs com suporte.

### <a name="azure-cosmos-db-api"></a>API do Azure Cosmos DB
O exemplo a seguir mostra como criar um contêiner (coleção) usando a API do Azure Cosmos DB. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Você pode ler um item (documento) usando o método `GET` na API REST ou usando `ReadDocumentAsync` em um dos SDKs.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API do MongoDB
Com a API do MongoDB, você pode criar uma coleção fragmentada por meio de sua ferramenta, driver ou SDK favorito. Neste exemplo, usamos o Shell do Mongo para a criação da coleção.

No Shell do Mongo:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Resultados:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API de Tabela

Com a API de Tabela, você especifica a taxa de transferência para tabelas na configuração appSettings do seu aplicativo.

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Em seguida, você cria uma tabela usando o SDK do Armazenamento de Tabelas do Azure. A chave de partição é criada implicitamente como o valor `PartitionKey`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

Você pode recuperar uma única entidade usando o trecho a seguir:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Para obter mais informações, consulte [Desenvolver com a API de Tabela](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>Graph API

Com a API do Graph, você deve usar o portal ou a CLI do Azure para criar contêineres. Como alternativa, uma vez que o Azure Cosmos DB tem vários modelos, você pode usar um dos outros modelos para criar e dimensionar seu contêiner de gráfico.

Você pode ler qualquer vértice ou borda usando a chave de partição e a ID no Gremlin. Por exemplo, para um gráfico com uma região ("EUA") como a chave de partição e "Seattle" como a chave de linha, você pode encontrar um vértice usando a seguinte sintaxe:

```
g.V(['USA', 'Seattle'])
```

Você pode referenciar uma borda usando a chave de partição e a chave de linha.

```
g.E(['USA', 'I5'])
```

Para obter mais informações, veja [Suporte de Gremlin para Azure Cosmos DB](gremlin-support.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Design de particionamento
Para dimensionar de forma eficiente com o BD Cosmos do Azure, você precisa escolher uma boa chave de partição quando criar seu contêiner. Há dois aspectos principais a considerar ao escolher uma chave de partição:

* **Limite de consulta e transações**. Sua escolha de chave de partição deve equilibrar a necessidade de habilitar o uso de transações em relação à necessidade de distribuir suas entidades por várias chaves de partição para garantir uma solução escalonável. Por um lado, você pode definir a mesma chave de partição para todos os seus itens, mas isso pode limitar a escalabilidade da sua solução. Por outro lado, você pode atribuir uma chave de partição exclusiva para cada item. Essa opção é altamente escalonável, mas impede que você use transações entre documentos por meio de procedimentos armazenados e gatilhos. Uma chave de partição ideal é aquela que permite o uso de consultas eficientes, e que tenha cardinalidade suficiente para garantir que sua solução seja escalonável. 
* **Sem gargalos de armazenamento e desempenho**. É importante escolher uma propriedade que permita que as gravações sejam distribuídas entre vários valores distintos. Solicitações para a mesma chave de partição não podem ultrapassar a taxa de transferência de uma única partição e sofrem restrições. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" dentro de seu aplicativo. Como todos os dados de uma única chave de partição devem ser armazenados em uma partição, você deve evitar chaves de partição com grandes volumes de dados para o mesmo valor. 

Vejamos alguns cenários do mundo real e boas chaves de partição para cada um deles:
* Se você estiver implementando um perfil do usuário de back-end, a ID de usuário será uma boa opção para a chave de partição.
* Se você estiver armazenando dados de IoT, por exemplo, o estado do dispositivo, uma ID do dispositivo será uma boa opção para a chave de partição.
* Se você estiver usando o Azure Cosmos DB para registrar em log dados de série temporal, a ID do processo ou o nome do host serão boas opções para a chave de partição.
* Se você tiver uma arquitetura de multilocatário, a ID do locatário será uma boa opção para a chave de partição.

Em alguns casos de uso, como perfis de usuário e IoT, a chave de partição pode ser igual à sua ID (chave de documento). Em outros, como os dados de série temporal, você pode ter uma chave de partição que é diferente da ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Dados de particionamento e registro em log/da série temporal
Um dos casos de uso comuns do Azure Cosmos DB é para registro em log e telemetria. É importante escolher uma boa chave de partição, já que você pode precisar ler/gravar enormes volumes de dados. A escolha depende das suas taxas de leitura e gravação e dos tipos de consultas que você pretende executar. Aqui estão algumas dicas sobre como escolher uma boa chave de partição:

* Se o caso de uso envolve uma pequena taxa de gravações que se acumulam durante longo período de tempo e você precisa consultar por intervalos de carimbos de data/hora e outros filtros, use um rollup de carimbo de data/hora. Por exemplo, uma boa abordagem é usar a data como uma chave de partição. Com essa abordagem, você pode consultar todos os dados para uma data a partir de uma única partição. 
* Se sua carga de trabalho tiver uma escrita pesada, o que é mais comum, use uma chave de partição que não seja baseada em carimbo de data/hora. Com essa abordagem, o Azure Cosmos DB pode distribuir gravações uniformemente por várias partições. Aqui, um nome do host, ID do processo, ID da atividade ou outra propriedade com alta cardinalidade é uma boa opção. 
* A outra abordagem é híbrida, onde você tem vários contêineres, um para cada dia/mês e a chave de partição é uma propriedade granular, como nome do host. Essa abordagem tem a vantagem de que você pode definir diferentes taxas de transferência com base na janela de tempo. Por exemplo, o contêiner para o mês atual é configurado com maior taxa de transferência porque ele serve leituras e gravações. Meses anteriores são provisionados com uma taxa de transferência menor porque só servem leituras.

### <a name="partitioning-and-multitenancy"></a>Particionamento e multilocação
Se você estiver implementando um aplicativo multilocatário usando o Azure Cosmos DB, haverá dois padrões populares: uma chave de uma partição por locatário e um contêiner por locatário. Aqui estão os prós e contras de cada um:

* **Chave de uma partição por locatário**. Nesse modelo, os locatários são colocados em um único contêiner. No entanto, consultas e inserções de itens em um único locatário podem ser executadas em relação a uma única partição. Você também pode implementar a lógica transacional em todos os itens dentro de um locatário. Como vários locatários compartilham um contêiner, você pode economizar custos de armazenamento e taxa de transferência realizando o pooling de recursos para locatários em um único contêiner em vez de provisionar capacidade extra para cada locatário. A desvantagem é que você não tem o isolamento de desempenho por locatário. Aumentos de taxa de transferência/desempenho se aplicam a todo o contêiner, versus aumentos direcionados para locatários.
* **Um contêiner por locatário**. Nesse modelo, cada locatário tem seu próprio contêiner, e você poderá reservar o desempenho por locatário. Com os novos preços de provisionamento do Azure Cosmos DB, esse modelo é mais econômico para aplicativos multilocatário com um número pequeno de locatários.

Você também pode usar uma abordagem de combinação/em camadas que coloca os locatários pequenos juntos e migra os locatários maiores para seu próprio contêiner.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, fornecemos uma visão geral dos conceitos e práticas recomendadas para o particionamento com qualquer API do Azure Cosmos DB. 

* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).



