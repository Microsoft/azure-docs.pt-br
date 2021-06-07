---
title: Perguntas frequentes sobre a API de Tabela no Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre a API de Tabela no Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341223"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a API de Tabela no Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

A API de Tabela do Azure Cosmos DB está disponível no [portal do Azure](https://portal.azure.com), mas primeiro você deve se inscrever em uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de API de Tabela do Azure Cosmos DB à sua assinatura e, em seguida, adicionar tabelas à conta. Você pode encontrar linguagens com suporte e inícios rápidos associados na [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>API de Tabela no Azure Cosmos DB versus armazenamento da Tabela do Azure

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Onde a API de Tabela não é idêntica com comportamento de armazenamento da Tabela do Azure?

Há algumas diferenças de comportamento de que os usuários provenientes do armazenamento de Tabela do Azure que desejam criar tabelas com a API de Tabela do Azure Cosmos DB devem estar cientes:

* A API de Tabela do Azure Cosmos DB usa um modelo de capacidade reservada para assegurar o desempenho garantido, mas isso significa que se paga pela capacidade assim que a tabela é criada, mesmo se a capacidade não estiver sendo usada. Com o armazenamento da Tabela do Azure, paga-se apenas pela capacidade que é usada. Isso ajuda a explicar por que a API de Tabela pode oferecer um SLA com leitura de 10 ms e gravação de 15 ms no 99º percentil enquanto o armazenamento de Tabela do Azure oferece um SLA de 10 segundos. Mas, como consequência, com tabelas da API de Tabela, até mesmo tabelas vazias sem quaisquer solicitações custam dinheiro para que se garanta que a capacidade esteja disponível para tratar de quaisquer solicitações feitas a elas no SLA oferecido pelo Azure Cosmos DB.

* Os resultados de consulta retornados pela API de Tabela não são classificados em ordem de chave de linha/chave de partição como é feito no armazenamento de Tabela do Azure.

* As chaves de linha só podem ter até 255 bytes.

* Os lotes só podem ter até 2 MBs.

* Não há suporte para CORS no momento.

* Os nomes de tabela no armazenamento de Tabela do Azure não diferenciam maiúsculas de minúsculas, mas estão na API de Tabela do Azure Cosmos DB.

* Alguns dos formatos internos do Azure Cosmos DB para a codificação de informações, como campos binários, atualmente não são tão eficientes quanto se desejaria. Portanto, isso pode causar limitações inesperadas no tamanho dos dados. Por exemplo, no momento você não poderia usar todo o 1 MB de uma entidade de tabela para armazenar dados binários, pois a codificação aumenta o tamanho dos dados.

* A “ID” do nome da propriedade de entidade não tem suporte no momento.

* TableQuery TakeCount não está limitado a 1.000.

* Em termos da API REST, há várias opções de pontos de extremidade/consulta que não são suportados pela API de Tabela do Azure Cosmos DB:

  | Métodos de REST | Opção de consulta/ponto de extremidade de REST | URLs de documento | Explicação |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Definir propriedades de serviço de tabela](/rest/api/storageservices/set-table-service-properties) e [Obter propriedades do serviço de tabela](/rest/api/storageservices/get-table-service-properties) | Esse ponto de extremidade é usado para definir as regras do CORS, a configuração de armazenamento da análise e as configurações de registro em log. Não há suporte para o CORS atualmente, e análises e registros em log são tratados de maneira diferente no Azure Cosmos DB em relação às Tabelas de Armazenamento do Azure |
  | OPÇÕES | `/<table-resource-name>` | [Solicitação de tabela preliminar de CORS ](/rest/api/storageservices/preflight-table-request) | Isso faz parte do CORS para o qual o Azure Cosmos DB não dá suporte atualmente. |
  | GET | `/?restype=service@comp=stats` | [Obter estatísticas do serviço de tabela](/rest/api/storageservices/get-table-service-stats) | Fornece informações sobre quão rapidamente os dados estão replicando entre primários e secundários. Isso não é necessária no Cosmos DB, uma vez que a replicação é parte de gravações. |
  | GET, PUT | `/mytable?comp=acl` | [Obter ACL da tabela](/rest/api/storageservices/get-table-acl) e [Definir ACL da tabela](/rest/api/storageservices/set-table-acl) | Isso obtém e define as políticas de acesso armazenadas usadas para gerenciar assinaturas de acesso compartilhado (SAS). Embora haja suporte para SAS, elas são definidas e gerenciadas de modo diferente. |

* A API de Tabela do Azure Cosmos DB só dá suporte ao formato JSON, ao ATOM não.

* Embora o Azure Cosmos DB dê suporte a assinaturas de acesso compartilhado (SAS), ele não dá suporte a determinadas políticas, especificamente as relacionadas a operações de gerenciamento, como o direito de criar novas tabelas.

* Particularmente para o SDK do .NET em, há algumas classes e métodos para os quais Azure Cosmos DB não dá suporte atualmente.

  | Classe | Método sem suporte |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties\* |
  |                  | \*ServiceStats\* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (essa classe foi preterida) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>É necessário ter um novo SDK para usar a API de Tabela?

Não, SDKs de armazenamento existentes ainda devem funcionar. No entanto, recomenda-se que um sempre sejam obtidos os SDKs mais recentes para ter o melhor suporte e, em muitos casos, desempenho superior. Consulte a lista de linguagens disponíveis na [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de conexão que preciso usar para me conectar à API de Tabela?

A cadeia de conexão é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Você pode obter a cadeia de conexão na página Cadeia de Conexão no Portal do Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como substituo as definições de configuração das opções de solicitação no SDK do .NET para a API de Tabela?

Algumas configurações são tratadas no método CreateCloudTableClient e outras por meio do app.config na seção appSettings no aplicativo cliente. Para obter informações sobre definições de configuração, consulte [Recursos do Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Há alterações para clientes que estão usando SDKs de armazenamento existentes da Tabela do Azure?

Nenhum. Não há nenhuma alteração para clientes novos ou existentes que estejam usando os SDKs de armazenamento de Tabela do Azure existentes.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como fazer para exibir dados de tabela que são armazenados no Azure Cosmos DB para uso com a API de Tabela?

Use o portal do Azure para procurar os dados. Você também pode usar o código da API de Tabela ou as ferramentas mencionadas na próxima resposta.

### <a name="which-tools-work-with-the-table-api"></a>Quais ferramentas funcionam com a API de Tabela?

Você pode usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Ferramentas que têm a flexibilidade para aceitar uma cadeia de conexão no formato especificado anteriormente podem dar suporte à nova API de Tabela. Uma lista das ferramentas de tabela é fornecida na página [Ferramentas de cliente do Armazenamento do Azure](../storage/common/storage-explorers.md).

### <a name="is-the-concurrency-on-operations-controlled"></a>A simultaneidade nas operações é controlada?

Sim, a simultaneidade otimista é fornecida com o uso do mecanismo de Etag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Há suporte para o modelo de consulta OData em entidades?

Sim, a API de Tabela dá suporte às consultas OData e LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso conectar ao Armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB lado a lado no mesmo aplicativo?

Sim, você pode se conectar criando duas instâncias separadas do CloudTableClient, cada uma apontando para seu próprio URI pela cadeia de conexão.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como fazer para migrar um aplicativo do armazenamento de Tabelas do Azure existente para essa oferta?

O [AzCopy](../storage/common/storage-use-azcopy-v10.md) e a [Ferramenta de migração de dados do Azure Cosmos DB](import-data.md) têm suporte.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como a expansão do tamanho do armazenamento é feita nesse serviço se, por exemplo, eu começar com *n* GB de dados e meus dados aumentarem para 1 TB ao longo do tempo?

O Azure Cosmos DB foi projetado para fornecer armazenamento ilimitado com o uso da escala horizontal. O serviço pode monitorar e efetivamente aumentar seu armazenamento.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como fazer para monitorar a oferta da API de Tabela?

Você pode usar o painel **Métrica** da API de Tabela para monitorar as solicitações e o uso do armazenamento.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como fazer para calcular a produtividade de que preciso?

Você pode usar o avaliador de capacidade para calcular o TableThroughput necessário para as operações. Para obter mais informações, consulte [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Estimar Unidades de Solicitação e Armazenamento de Dados). Em geral, você pode mostrar a entidade como JSON e fornecer os números para as operações.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso usar o SDK da API de Tabela localmente com o emulador?

Não no momento.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Um aplicativo que já tenho pode trabalhar com a API de Tabela?

Sim, há suporte para a mesma API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Preciso migrar meus aplicativos de armazenamento de Tabela do Azure existentes para o SDK caso não deseje usar os recursos da API de Tabela?

Não, você pode criar e usar os ativos de armazenamento da Tabela do Azure sem nenhum tipo de interrupção. No entanto, se você não usar a API de Tabela, não poderá se beneficiar do índice automático, da opção de consistência adicional ou da distribuição global.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Como fazer para adicionar a replicação dos dados na API de Tabela em mais de uma região do Azure?

Você pode usar as [configurações de replicação global](tutorial-global-distribution-sql-api.md#portal) do portal do Azure Cosmos DB para adicionar regiões que servem para o aplicativo. Para desenvolver um aplicativo distribuído globalmente, você deve também adicionar seu aplicativo com as informações de PreferredLocation definidas como a região local, a fim de fornecer uma baixa latência de leitura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como faço para alterar a região de gravação primária da conta na API de Tabela?

Você pode usar o painel do portal de replicação global do Azure Cosmos DB para adicionar uma região e, em seguida, fazer failover para a região desejada. Para obter instruções, consulte [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como fazer para configurar minhas regiões de leitura preferenciais com baixa latência ao distribuir meus dados?

Para ajudar a leitura do local, use a chave PreferredLocation no arquivo app.config. Para aplicativos existentes, a API de Tabela gera um erro se LocationMode for definida. Remova esse código, porque a API de Tabela obtém essas informações do arquivo app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como devo pensar sobre os níveis de consistência na API de Tabela?

O Azure Cosmos DB fornece compensações bem fundamentadas entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência para os desenvolvedores da API de Tabela para que você possa escolher o modelo de consistência certo no nível da tabela e fazer solicitações individuais ao consultar os dados. Quando um cliente se conecta, ele pode especificar um nível de consistência. Você pode alterar o nível por meio do argumento da consistencyLevel do CreateCloudTableClient.

A API de Tabela fornece leituras de baixa latência com a “Leitura de suas próprias gravações” com a consistência de desatualização limitada como padrão. Para obter mais informações, consulte [Níveis de consistência](consistency-levels.md).

Por padrão, o armazenamento de Tabelas do Azure fornece uma coerência Forte em uma região e uma Eventual nas localizações secundárias.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>A API de Tabela do Azure Cosmos DB oferece mais níveis de consistência que o armazenamento de Tabela do Azure?

Sim, para obter informações sobre como se beneficiar da natureza distribuída do Azure Cosmos DB, consulte [Níveis de consistência](consistency-levels.md). Como são fornecidas garantias para os níveis de consistência, você pode usá-los com confiança.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global é habilitada, quanto tempo é necessário para replicar os dados?

O Azure Cosmos DB confirma os dados de maneira duradoura na região local e efetua o push dos dados para outras regiões imediatamente em questão de milissegundos. Essa replicação depende apenas do RTT (tempo de ida e volta) do data center. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, consulte [Azure Cosmos DB: um serviço de banco de dados distribuído globalmente no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência de solicitação de leitura pode ser alterado?

Com o Azure Cosmos DB, você pode definir o nível de consistência no nível do contêiner (na tabela). Usando o SDK do .NET, você pode alterar o nível fornecendo o valor para a chave TableConsistencyLevel no arquivo app.config. Os valores possíveis são: Forte, desatualização limitada, sessão, prefixo consistente e eventual. Para obter mais informações, consulte [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](consistency-levels.md). A ideia da chave é que você não pode definir o nível de consistência da solicitação em mais do que a configuração da tabela. Por exemplo, você não pode definir o nível de consistência para a tabela como Eventual e o nível de consistência de solicitação como Forte.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como a API de Tabela lida com o failover caso uma região fique inativa?

A API de Tabela utiliza a plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o tempo de inatividade do data center, habilite pelo menos mais uma região para a conta no Portal do Azure Cosmos DB [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md). Defina a prioridade da região usando o portal [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md).

Você pode adicionar quantas regiões quiser para a conta e controlar para onde ela pode fazer o failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo lá também. Quando você fizer isso, os clientes não terão o tempo de inatividade. O [SDK mais recente do cliente .NET](table-sdk-dotnet.md) tem hospedagem automática, mas outros SDKs não têm. Ou seja, ele pode detectar a região que está inoperante e automaticamente fazer o failover para a nova região.

### <a name="is-the-table-api-enabled-for-backups"></a>A API de Tabela está habilitada para backups?

Sim, a API de Tabela utiliza a plataforma do Azure Cosmos DB para backups. Os backups são feitos automaticamente. Para obter mais informações, consulte [Backup e restauração online com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A API de Tabela indexa todos os atributos de uma entidade por padrão?

Sim, por padrão, todos os atributos de uma entidade são indexados. Para obter mais informações, veja [Azure Cosmos DB: políticas de indexação](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Isso significa que não é necessário criar mais de um índice para atender às consultas?

Sim, a API de Tabela do Azure Cosmos DB fornece a indexação automática de todos os atributos sem nenhuma definição de esquema. Essa automação libera os desenvolvedores para se concentrarem no aplicativo, em vez de na criação e no gerenciamento de índices. Para obter mais informações, veja [Azure Cosmos DB: políticas de indexação](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Posso alterar a política de indexação?

Sim, você pode alterar a política de indexação fornecendo a definição de índice. Você precisa codificar e escapar adequadamente as configurações.

Para o SDKs não .NET, a política de indexação só pode ser definida no portal no **Data Explorer**, navegue até a tabela específica que deseja alterar e depois até **Escala e Configurações**-> Política de Indexação, faça as alterações desejadas e depois selecione **Salvar**.

No SDK do .NET, pode ser enviado no arquivo app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>O Azure Cosmos DB como plataforma parece ter muitas funcionalidades, como classificação, agregações, hierarquia e outras. Vocês adicionarão esses recursos à API de tabela?

A API de Tabela fornece a mesma funcionalidade de consulta que o armazenamento de Tabelas do Azure. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Para obter mais informações, consulte [Consultas SQL](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando devo alterar a TableThroughput da API de Tabela?

Você deve alterar TableThroughput quando uma das seguintes condições se aplicar:

* Você estiver executando uma ETL (extração, transformação e carregamento) de dados ou desejar carregar muitos dados em um curto período de tempo.
* Você precisa de mais taxa de transferência do contêiner ou de um conjunto de contêineres no back-end. Por exemplo, você verá que a produtividade usada é maior do que a provisionada e você está sendo limitado. Para obter mais informações, consulte [Definir a taxa de transferência para contêineres do Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Posso escalar verticalmente ou reduzir verticalmente a produtividade da minha tabela da API de Tabela (versão prévia)?

Sim, você pode usar o painel de escala do portal do Azure Cosmos DB para dimensionar a produtividade. Para obter mais informações, veja [Configurar produtividade](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Há uma configuração padrão de TableThroughput para as tabelas recém-provisionadas?

Sim, se você não substituir a TableThroughput por meio do app.config e não usar um contêiner pré-criado no Azure Cosmos DB, o serviço criará uma Tabela com uma produtividade de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Há alguma alteração de preço para clientes atuais do serviço de armazenamento de Tabela do Azure?

Nenhum. Não há nenhuma alteração no preço para clientes de armazenamento de Tabela do Azure existentes.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como o preço é calculado para a API de Tabela?

O preço depende da TableThroughput alocada.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Como lidar com a limitação de taxa nas tabelas da oferta da API de Tabela?

Se a taxa de solicitação for mais do que a capacidade da taxa de transferência provisionada para o contêiner ou um conjunto de contêineres subjacente, você receberá um erro, e o SDK tentará realizar a chamada novamente aplicando a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que eu preciso escolher uma taxa de transferência além da PartitionKey e da RowKey para aproveitar a oferta da API de Tabela do Azure Cosmos DB?

O Azure Cosmos DB define uma produtividade padrão para o contêiner caso você não forneça uma no arquivo app.config ou por meio do portal.

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Configurar TableThroughput garante que você obtenha a produtividade e a latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso operacional.

Usando a especificação de produtividade, você pode alterá-la de forma elástica para se beneficiar da sazonalidade de seu aplicativo, atender às necessidades de produtividade e reduzir custos.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>O armazenamento da Tabela do Azure tem custado pouco para mim, pois pago apenas para armazenar os dados e raramente realizo consultas. A oferta da API de Tabela do Azure Cosmos DB parece estar me cobrando mesmo sem eu ter realizado nenhuma transação ou armazenado nada. Você pode explicar?

O Azure Cosmos DB foi projetado para ser um sistema baseado em SLA distribuído globalmente com garantias de disponibilidade, latência e produtividade. Quando você reserva a produtividade no Azure Cosmos DB, ela fica garantida, diferentemente da produtividade de outros sistemas. O Azure Cosmos DB fornece recursos adicionais que os clientes solicitaram, como índices secundários e distribuição global.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Eu nunca recebo uma notificação de “cota cheia” (indicando que uma partição está cheia) quando realizo a ingestão de dados no armazenamento de Tabelas do Azure. Com a API de Tabela, eu recebo essa mensagem. Essa oferta está me limitando e me forçando a alterar meu aplicativo existente?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Para assegurar o desempenho premium garantido, certifique-se de que seu índice e tamanho de dados sejam gerenciáveis e escalonáveis. O limite de 20 GB no número de entidades ou itens por chave de partição é garantir que possamos fornecer um ótimo desempenho de pesquisa e consulta. Para garantir que seu aplicativo dimensiona bem, mesmo para o Armazenamento do Microsoft Azure, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey e RowKey ainda são necessárias na API de Tabela?

Sim. Uma vez que a área de superfície da API de Tabela é semelhante à do SDK de armazenamento de Tabelas do Azure, a chave de partição oferece uma maneira eficiente de distribuir os dados. A chave de linha é exclusiva nessa partição. A chave de linha deve estar presente e não pode ser nula como no SDK Standard. O comprimento da RowKey é de 255 bytes e da PartitionKey é de 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro da API de Tabela?

O armazenamento da Tabela do Azure e da API de Tabela do Azure Cosmos DB usam os mesmos SDKs, então a maioria dos erros serão os mesmos.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que recebo um limite quando tento criar várias tabelas uma após a outra na API de Tabela?

O Azure Cosmos DB é um sistema baseado em SLA que fornece garantia de latência, produtividade, disponibilidade e consistência. Como ele é um sistema provisionado, ele reserva recursos para garantir esses requisitos. A rápida taxa de criação de tabelas é detectada e limitada. É recomendável que você examine a taxa de criação de tabelas e a reduza para menos de cinco por minuto. Lembre-se de que a API de Tabela é um sistema provisionado. No momento em que você a provisionar, começará a pagar por ela.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como fazer para fornecer comentários sobre o SDK ou bugs?

Você pode compartilhar seus comentários em qualquer uma das seguintes maneiras:

* [Voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Página de perguntas e respostas da Microsoft](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). O Stack Overflow é o melhor para perguntas sobre programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possíveis, tornando a pergunta clara e possível de responder](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Próximas etapas

* [Compilar um aplicativo de API de Tabela com o SDK do .NET e o Azure Cosmos DB](create-table-dotnet.md)
* [Criar um aplicativo Java para gerenciar os dados de API de Tabela do Azure Cosmos DB](create-table-java.md)