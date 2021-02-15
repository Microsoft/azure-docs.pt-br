---
title: Perguntas frequentes sobre o API do Cassandra para Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre o API do Cassandra para Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030825"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre o API do Cassandra no Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo descreve as diferenças de funcionalidade entre o Apache Cassandra e o API do Cassandra no Azure Cosmos DB. Ele também fornece respostas para perguntas frequentes sobre o API do Cassandra no Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Principais diferenças entre o Apache Cassandra e o API do Cassandra

- O Apache Cassandra recomenda um limite de 100 MB no tamanho de uma chave de partição. O API do Cassandra para Azure Cosmos DB permite até 20 GB por partição.
- O Apache Cassandra permite que você desabilite confirmações duráveis. Você pode ignorar a gravação no log de confirmação e ir diretamente para o memtables. Isso pode levar à perda de dados se o nó falhar antes que memtables sejam liberados para SSTables no disco. Azure Cosmos DB sempre faz confirmações duráveis para ajudar a evitar a perda de dados.
- O Apache Cassandra pode ver um desempenho reduzido se a carga de trabalho envolve muitas substituições ou exclusões. O motivo é a marca para exclusão que a carga de trabalho de leitura precisa ignorar para buscar os dados mais recentes. A API do Cassandra não verá um desempenho de leitura reduzido quando a carga de trabalho tiver muitas substituições ou exclusões.
- Durante cenários de cargas de trabalho de substituição altas, a compactação precisa ser executada para mesclar SSTables no disco. (Uma mesclagem é necessária porque as gravações do Apache Cassandra são acrescentadas somente. Várias atualizações são armazenadas como entradas de SSTable individuais que precisam ser mescladas periodicamente). Essa situação também pode levar a um desempenho de leitura reduzido durante a compactação. Esse impacto no desempenho não ocorre na API do Cassandra porque a API não implementa a compactação.
- A definição de um fator de replicação de 1 é possível com o Apache Cassandra. No entanto, ele leva à baixa disponibilidade se o único nó com os dados ficar inativo. Isso não é um problema com o API do Cassandra para Azure Cosmos DB porque sempre há um fator de replicação de 4 (quorum de 3).
- Adicionar ou remover nós no Apache Cassandra requer intervenção manual, juntamente com alto uso da CPU no novo nó, enquanto os nós existentes movem alguns dos intervalos de token para o novo nó. Essa situação é a mesma quando você está encerrando um nó existente. No entanto, a API do Cassandra é dimensionada sem quaisquer problemas observados no serviço ou aplicativo.
- Não é necessário definir **num_tokens** em cada nó no cluster como no Apache Cassandra. Azure Cosmos DB gerencia totalmente nós e intervalos de token.
- O API do Cassandra é totalmente gerenciado. Você não precisa dos comandos **nodetool** , como reparo e descomissionamento, que são usados no Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>A qual versão de protocolo o API do Cassandra dá suporte?

O API do Cassandra para Azure Cosmos DB dá suporte ao CQL versão 3. x. Sua compatibilidade com o CQL é baseada no [repositório GitHub público do Apache Cassandra](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Se você tiver comentários sobre como dar suporte a outros protocolos, avise-nos por meio [dos comentários de voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Por que escolher a taxa de transferência para uma tabela é um requisito?

Azure Cosmos DB define a taxa de transferência padrão para seu contêiner com base em onde você cria a tabela: portal do Azure ou CQL.

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores nas operações. Essas garantias são possíveis quando o mecanismo pode impor a governança nas operações do locatário. Configurar a taxa de transferência assegura que você obtenha taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.
Você pode [alterar a taxa de transferência](manage-scale-cassandra.md) de forma elástica para se beneficiar da sazonalidade de seu aplicativo e economizar custos.

O conceito de taxa de transferência é explicado no artigo [Unidades de solicitação no Azure Cosmos DB](request-units.md). A taxa de transferência de uma tabela é igualmente distribuída entre as partições físicas subjacentes.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Qual é a taxa de transferência de uma tabela criada por meio de CQL?

O Azure Cosmos DB usa unidades de solicitação por segundo (RU/s) como uma moeda para fornecer a taxa de transferência. As tabelas criadas por meio de CQL têm 400 RU por padrão. Você pode alterar a RU da portal do Azure.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando a taxa de transferência é usada?

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores nas operações. Essas garantias são possíveis quando o mecanismo pode impor a governança nas operações do locatário. Configurar a taxa de transferência assegura que você obtenha taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.

Quando você passa por essa capacidade, recebe a seguinte mensagem de erro que indica que a capacidade foi usada:

**0x1001 sobrecarregado: a solicitação não pode ser processada porque "a taxa de solicitação é grande"** 

É essencial ver quais operações (e seu volume) causam esse problema. Você pode ter uma ideia da capacidade consumida de passar pela capacidade provisionada com métricas no portal do Azure. Em seguida, você precisa garantir que a capacidade seja consumida quase igualmente entre todas as partições subjacentes. Se você vir que uma partição está consumindo a maior parte da taxa de transferência, você tem distorção de carga de trabalho.

Há métricas disponíveis que mostram como a taxa de transferência é usada em horas, mais de dias e por sete dias, entre partições ou agregadas. Para obter mais informações, consulte [monitorando e Depurando com métricas em Azure Cosmos DB](use-metrics.md).

Logs de diagnóstico são explicados no artigo [Logs de diagnóstico do Azure Cosmos DB](./monitor-cosmos-db.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>A chave primária mapeia para o conceito de chave de partição do Azure Cosmos DB?

Sim, a chave de partição é usada para posicionar a entidade no local certo. No Azure Cosmos DB, ele é usado para localizar a partição lógica correta que é armazenada em uma partição física. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partitioning-overview.md). A vantagem essencial aqui é que uma partição lógica não deve passar pelo limite de 20 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação de que uma partição está cheia?

Azure Cosmos DB é um sistema baseado no SLA (contrato de nível de serviço). Ele fornece escala ilimitada, com garantias de latência, taxa de transferência, disponibilidade e consistência. Esse armazenamento ilimitado é baseado na escala horizontal dos dados, usando o particionamento como o conceito-chave. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partitioning-overview.md).

Você deve aderir ao limite de 20 GB no número de entidades ou itens por partição lógica. Para garantir que seu aplicativo dimensiona bem, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a. Esse erro pode vir somente se os dados estiverem distorcidos: ou seja, você tem muitos dados para uma chave de partição (mais de 20 GB). Você pode encontrar a distribuição de dados usando o portal de armazenamento. A maneira de corrigir esse erro é recriar a tabela e escolher uma primária granular (chave de partição), o que permite uma melhor distribuição dos dados.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Posso usar o API do Cassandra como um repositório de valor de chave com milhões ou bilhões de chaves de partição?

O Azure Cosmos DB pode armazenar dados ilimitados expandindo o armazenamento. Esse armazenamento é independente da taxa de transferência. Sim, você sempre pode usar a API do Cassandra apenas para armazenar e recuperar chaves e valores especificando a chave primária/de partição correta. Essas chaves individuais obtêm sua própria partição lógica e ficam acima de uma partição física sem problemas.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Posso criar mais de uma tabela com o API do Cassandra?

Sim, é possível criar mais de uma tabela com a API do Cassandra. Cada uma dessas tabelas é tratada como unidade de taxa de transferência e armazenamento.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Posso criar mais de uma tabela sucessivamente?

Azure Cosmos DB é o sistema controlado por recursos para atividades de plano de controle e de dados. Contêineres, como coleções e tabelas, são entidades de tempo de execução provisionadas para uma determinada capacidade de taxa de transferência. A criação desses contêineres em sucessão rápida não é uma atividade esperada e pode ser limitada. Se você tiver testes que descartam ou criar tabelas imediatamente, tente fazer o espaçamento deles.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Qual é o número máximo de tabelas que posso criar?

Não há nenhum limite físico no número de tabelas. Se você tiver um grande número de tabelas (em que o tamanho constante total vai mais de 10 TB de dados) que precisam ser criados, não os dezenas ou centenas usuais, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Qual é o número máximo de keyspaces que posso criar?

Não há nenhum limite físico para o número de keyspaces porque eles são contêineres de metadados. Se você tiver um grande número de keyspaces, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Posso colocar muitos dados depois de iniciar a partir de uma tabela normal?

Sim. Supondo partições distribuídas uniformemente, a capacidade de armazenamento é gerenciada automaticamente e aumenta à medida que você envia mais dados por push. Portanto, você pode importar com segurança tantos dados quantos forem necessários sem gerenciar e provisionar nós e muito mais. Mas se você estiver prevendo uma grande quantidade de aumentos de dados imediatos, faz mais sentido [provisionar diretamente para a taxa de transferência antecipada](set-throughput.md) em vez de começar a diminuir e aumentar imediatamente.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Posso usar as configurações de arquivo YAML para configurar o comportamento da API?

O API do Cassandra para Azure Cosmos DB fornece compatibilidade em nível de protocolo para executar operações. Ela oculta a complexidade de gerenciamento, monitoramento e configuração. Como desenvolvedor/usuário, você não precisa se preocupar com a disponibilidade, marcas para exclusão, cache de chave, cache de linha, filtro de flor e uma infinidade de outras configurações. O API do Cassandra se concentra em fornecer o desempenho de leitura e gravação de que você precisa sem a sobrecarga de configuração e gerenciamento.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>O API do Cassandra oferece suporte a adição de nó, status de cluster e comandos de status de nó?

O API do Cassandra simplifica o planejamento de capacidade e a resposta às demandas de elasticidade para taxa de transferência e armazenamento. Com o Azure Cosmos DB, você provisiona a taxa de transferência de que precisa. Em seguida, você pode dimensioná-lo para cima e para baixo várias vezes no dia, sem se preocupar com a adição, exclusão ou gerenciamento de nós. Você não precisa usar ferramentas para gerenciamento de nó e cluster.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece com várias definições de configuração para a criação de keyspace como simples/rede?

O Azure Cosmos DB fornece distribuição global pronta para uso por motivos de baixa latência e disponibilidade. Você não precisa configurar réplicas ou outras coisas. As gravações são sempre o quorum permanentemente confirmado em qualquer região em que você escreve, ao mesmo tempo em que fornece garantias de desempenho.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>O que acontece com várias configurações para metadados de tabela?

Azure Cosmos DB fornece garantias de desempenho para leituras, gravações e taxa de transferência. Portanto, você não precisa se preocupar em tocar em qualquer um dos parâmetros de configuração e manipulá-los acidentalmente. Essas configurações incluem filtro de flor, cache, chance de reparo de leitura, gc_grace e memtable_flush_period de compactação.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>O tempo de vida tem suporte para tabelas Cassandra?

Sim, há suporte para TTL.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Como posso monitorar a infraestrutura junto com a taxa de transferência?

O Azure Cosmos DB é um serviço de plataforma que ajuda a aumentar a produtividade e não se preocupar com o gerenciamento e monitoramento da infraestrutura. Por exemplo, você não precisa monitorar OS parâmetros status do nó, status da réplica, GC e so anteriormente com várias ferramentas. Você só precisa cuidar da taxa de transferência disponível nas métricas do portal para ver se você está ficando limitado e, em seguida, aumentar ou diminuir essa taxa de transferência. Você pode:

- Monitorar [SLAs](./monitor-cosmos-db.md)
- Usar [métricas](use-metrics.md)
- Usar [logs de diagnóstico](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Quais SDKs de cliente podem trabalhar com o API do Cassandra?

Os drivers de cliente do SDK do Apache Cassandra que usam CQLv3 foram usados para programas cliente. Se você tiver outros drivers que você usa ou se estiver enfrentando problemas, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Há suporte para chaves de partição compostas?

Sim, você pode usar a sintaxe regular para criar chaves de partição compostas.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar sstableloader para carregamento de dados?

Não, não há suporte para sstableloader.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Posso emparelhar um cluster apache Cassandra local com o API do Cassandra?

No momento, Azure Cosmos DB tem uma experiência otimizada para um ambiente de nuvem sem a sobrecarga das operações. Se você precisar de emparelhamento, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos trabalhando em uma oferta para ajudar a emparelhar o cluster local ou de nuvem Cassandra com o API do Cassandra para Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>O API do Cassandra fornece backups completos?

O Azure Cosmos DB fornece dois backups completos gratuitos feitos em intervalos de quatro horas em todas as APIs. Portanto, você não precisa configurar um agendamento de backup. 

Se você quiser modificar a retenção e a frequência, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ou gere um caso de suporte. As informações sobre a funcionalidade de backup são fornecidas no artigo [Backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como uma conta da API Cassandra lida com o failover caso uma região fique inativa?

O API do Cassandra emprestado da plataforma de Azure Cosmos DB distribuída globalmente. Para garantir que seu aplicativo possa tolerar o tempo de inatividade do datacenter, habilite pelo menos mais uma região para a conta na portal do Azure. Para obter mais informações, consulte [alta disponibilidade com Azure Cosmos DB](high-availability.md).

Você pode adicionar quantas regiões quiser para a conta e controlar para onde ela pode fazer o failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo lá também. Quando você fizer isso, os clientes não terão o tempo de inatividade.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>O API do Cassandra indexa todos os atributos de uma entidade por padrão?

Não. O API do Cassandra dá suporte a [índices secundários](cassandra-secondary-index.md), que se comportam de uma maneira semelhante ao Apache Cassandra. A API não indexa todos os atributos por padrão.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK da API Cassandra localmente com o emulador?

Sim, há suporte para isso. Você pode encontrar detalhes sobre como habilitar isso no artigo [usar o emulador de Azure Cosmos DB para desenvolvimento e teste local](local-emulator.md#cassandra-api) .


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Como posso migrar dados de clusters Apache Cassandra para Azure Cosmos DB?

Você pode ler sobre as opções de migração no tutorial [migrar seus dados para API do Cassandra no Azure Cosmos DB](cassandra-import-data.md) .


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Onde posso fornecer comentários sobre API do Cassandra recursos?

Deixe comentários por meio dos [comentários da voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Próximas etapas

- Introdução ao [dimensionamento elástico de uma conta de API do Cassandra de Azure Cosmos DB](manage-scale-cassandra.md).
