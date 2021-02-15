---
title: Perguntas frequentes sobre diferentes APIs no Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre o Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente. Aprenda sobre capacidade, níveis de desempenho e dimensionamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359822"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas frequentes sobre diferentes APIs no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típicos do Azure Cosmos DB?

O Azure Cosmos DB é uma ótima escolha para novos aplicativos Web, móveis, de jogos e IoT, em que a escala automática, o desempenho previsível, os tempos de resposta rápidos na ordem de milissegundos e a capacidade de consultar dados sem esquemas são importantes. O Azure Cosmos DB funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como o Azure Cosmos DB oferece um desempenho previsível?

Uma RU ([Unidade de Solicitação](request-units.md)) é a medida de taxa de transferência no Azure Cosmos DB. Uma taxa de transferência de 1 RU corresponde à taxa de transferência do GET de um documento de 1 KB. Toda operação no Azure Cosmos DB, incluindo leituras, gravações, consultas SQL e execuções de procedimentos armazenados, tem um valor determinístico de RU baseado na produtividade necessária para concluir a operação. Em vez de pensar em CPU, E/S, memória e como cada uma dessas medidas afeta a produtividade do seu aplicativo, você pode pensar em uma medida de RU única.

Você pode configurar cada contêiner do Azure Cosmos com taxa de transferência provisionada em termos de RUs da taxa de transferência por segundo. Em aplicativos de qualquer escala, você pode usar um parâmetro de comparação em solicitações individuais para medir seus valores de RU e provisionar um contêiner para manipular o total de unidades de solicitação em todas as solicitações. Também é possível aumentar ou reduzir verticalmente a produtividade de seu contêiner, conforme as necessidades do aplicativo mudam. Para obter mais informações sobre unidades de solicitação e para obter ajuda para determinar suas necessidades de contêiner, experimente a [calculadora de taxa de transferência](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como o Azure Cosmos DB dá suporte a vários modelos de dados, como chave/valor, colunar, documento e grafo?

Os modelos de dados chave/valor (tabela), colunar, documento e grafo têm suporte nativo devido ao design ARS (átomos, registros e sequências) no qual o Azure Cosmos DB é criado. Os átomos, os registros e as sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos já estão disponíveis (SQL, MongoDB, tabela e Gremlin) e outras específicas para modelos de dados adicionais estarão disponíveis no futuro.

O Azure Cosmos DB tem um mecanismo de indexação livre de esquemas, capaz de indexar automaticamente todos os dados que ingere sem a necessidade de qualquer esquema ou de índices secundários do desenvolvedor. O mecanismo apoia-se em um conjunto de layouts de índice lógico (invertido, colunar, árvore) que separam o layout de armazenamento do índice e dos subsistemas de processamento de consulta. O Cosmos DB também tem a capacidade de dar suporte a um conjunto de protocolos de transmissão e APIs de forma extensível e convertê-las com eficiência no modelo de dados principal (1) e nos layouts de índice lógico (2) tornando-o capaz de dar suporte a vários modelos de dados.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Posso usar várias APIs para acessar meus dados?

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Quando vários modelos significarem que o Azure Cosmos DB tem suporte para várias APIs e vários modelos de dados, APIs diferentes usarão formatos de dados diferentes para o protocolo de armazenamento e de conexão. Por exemplo: o SQL usa JSON; o MongoDB usa BSON; o Table usa o EDM; o Cassandra usa CQL; o Gremlin usa o formato JSON. Dessa forma, é recomendável usar a mesma API para todos os acessos aos dados em uma determinada conta.

Cada API opera de forma independente, exceto as API do Gremlin e do SQL, que são interoperáveis.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O Azure Cosmos DB está em conformidade com a HIPAA?

Sim, o Azure Cosmos DB está em conformidade com a HIPAA. A HIPAA estabelece os requisitos para o uso, a divulgação e a proteção de informações de integridade individualmente identificáveis. Para saber mais, confira o [Centro de Confiabilidade da Microsoft](/compliance/regulatory/offering-hipaa-hitech).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento do Azure Cosmos DB?

Não há limite para a quantidade total de dados que um contêiner pode armazenar no Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de produtividade do Azure Cosmos DB?

Não há limite para a quantidade total de produtividade para a qual um contêiner dá suporte no Azure Cosmos DB. A ideia principal é distribuir sua carga de trabalho aproximadamente de forma uniforme entre um número suficientemente grande de chaves de partição.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Os modos de conectividade Direta e Gateway são criptografados?

Sim, ambos os modos estão sempre totalmente criptografados.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa o Azure Cosmos DB?

Para obter detalhes, consulte a página [Detalhes de preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Os encargos pelo uso do Azure Cosmos DB são determinados pelo número de contêineres provisionados, pelo número de horas em que os contêineres estavam online e pela produtividade provisionada de cada contêiner.

### <a name="is-a-free-account-available"></a>Existe uma conta gratuita disponível?

Sim, você pode se inscrever para uma conta de tempo limitado sem custo adicional, sem nenhum compromisso. Para se inscrever, visite [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) ou leia mais em [Tente as Perguntas frequentes do Azure Cosmos DB](#try-cosmos-db).

Se você estiver começando com o Azure, é possível inscrever-se para uma [Conta gratuita do Azure](https://azure.microsoft.com/free/), que oferece 30 dias e um crédito para testar todos os serviços do Azure. Se você tem uma assinatura do Visual Studio, também está qualificado para receber [créditos Azure gratuitos](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usar em qualquer serviço do Azure.

Também use o [Emulador do Azure Cosmos DB](local-emulator.md) para desenvolver e testar seu aplicativo no local, gratuitamente, sem criar uma assinatura do Azure. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador do Azure Cosmos DB, você poderá passar a usar uma conta do Azure Cosmos DB na nuvem.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como fazer para obter mais ajuda com o Azure Cosmos DB?

Para fazer uma pergunta técnica, poste em um desses dois fóruns de perguntas e respostas:

* [Página de perguntas e respostas da Microsoft](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). O Stack Overflow é o melhor para perguntas sobre programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possíveis, tornando a pergunta clara e possível de responder](https://stackoverflow.com/help/how-to-ask).

Para solicitar novos recursos, crie uma nova solicitação em [Voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Experimente as assinaturas do Azure Cosmos DB

Agora você pode aproveitar uma experiência do Azure Cosmos DB gratuitamente, por tempo limitado, sem assinatura e sem compromisso. Para se inscrever para uma assinatura do tipo Experimente o Azure Cosmos DB gratuitamente, vá para [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) e use qualquer MSA (Conta pessoal da Microsoft). Esta assinatura é separada da [Avaliação gratuita do Azure](https://azure.microsoft.com/free/) e pode ser usada juntamente com uma Avaliação gratuita do Azure ou uma assinatura paga do Azure.

Experimente as assinaturas do Azure Cosmos DB é exibido no portal do Azure ao lado de outras assinaturas associadas à sua ID de usuário.

As seguintes condições se aplicam às assinaturas de Experimente o Azure Cosmos DB:

* O acesso à conta pode ser concedido a MSAs (Contas pessoais da Microsoft). Evite usar contas do Azure Active Directory (AD do Azure) ou contas que pertencem a locatários corporativos do Azure AD, elas podem ter limitações em vigor que poderiam bloquear a concessão de acesso.
* Um [contêiner de taxa de transferência provisionada](./set-throughput.md#set-throughput-on-a-container) por assinatura para contas SQL, API do Gremlin e Table.
* Até três [coleções de taxa de transferência provisionada](./set-throughput.md#set-throughput-on-a-container) por assinatura para contas do MongoDB.
* Um [banco de dados de taxa de transferência provisionada](./set-throughput.md#set-throughput-on-a-database) por assinatura. Os bancos de dados de taxa de transferência provisionada podem conter qualquer número de contêineres internamente.
* Capacidade de armazenamento de 10 GB.
* A replicação global está disponível atualmente nas seguintes [regiões do Azure](https://azure.microsoft.com/regions/): EUA Central, Norte da Europa e Sudeste Asiático
* Taxa de transferência máxima de 5.000 RU/s quando provisionadas no nível de contêiner.
* Taxa de transferência máxima de 20.000 RU/s quando provisionadas no nível de banco de dados.
* As assinaturas expiram após 30 dias e podem ser estendidas para um máximo de 31 dias no total. Depois de expirarem, as informações contidas são excluídas.
* Não é possível criar tíquetes de suporte do Azure para contas Experimente o Azure Cosmos DB; no entanto, é oferecido suporte para assinantes com planos de suporte existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar o Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como fazer para se inscrever no Azure Cosmos DB?

O Azure Cosmos DB está disponível no Portal do Azure. Primeiro, inscreva-se para uma assinatura do Azure. Depois de se inscrever, você poderá adicionar uma conta do Azure Cosmos DB à sua assinatura do Azure.

### <a name="what-is-a-primary-key"></a>O que é uma chave primária?

Uma chave primária é um token de segurança para acessar todos os recursos de uma conta. As pessoas que têm a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Tenha cuidado ao distribuir chaves primárias. A chave primária primária e a chave primária secundária estão disponíveis na folha **chaves** da [portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões para as quais PreferredLocations pode ser definido?

O valor de PreferredLocations pode ser definido para qualquer uma das regiões do Azure em que o Cosmos DB está disponível. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há algo que devo saber ao distribuir dados pelo mundo todo por meio dos data centers do Azure?

O Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página [Regiões do Azure](https://azure.microsoft.com/regions/). Como ele é o serviço principal, cada novo data center tem uma presença do Azure Cosmos DB.

Quando você definir uma região, lembre-se de que o Azure Cosmos DB respeita nuvens soberanas e governamentais. Ou seja, se você criar uma conta em uma [região soberana](https://azure.microsoft.com/global-infrastructure/), não poderá replicar fora da [região soberana](https://azure.microsoft.com/global-infrastructure/). Da mesma forma, você não pode habilitar a replicação em outros locais soberanos de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível mudar do provisionamento de produtividade no nível do contêiner para o provisionamento de produtividade no nível do banco de dados? Ou vice-versa

O provisionamento de produtividade no nível do contêiner e no nível do banco de dados são ofertas separadas e mudar entre elas requer a migração de dados de origem para destino. Isso significa que você precisa criar um novo banco de dados ou um novo contêiner e, em seguida, migrar os dados usando a [biblioteca do executor em massa](bulk-executor-overview.md) ou o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>O Azure CosmosDB dá suporte à análise de série temporal?

Sim, o Azure CosmosDB dá suporte à análise de série temporal; veja um exemplo do [padrão de série temporal](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). O exemplo mostra como usar o feed de alterações para criar exibições agregadas nos dados de série temporal. Você pode estender essa abordagem usando o streaming do spark ou outro processador de dados de streaming.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quais são as cotas de serviço e os limites de taxa de transferência do Azure Cosmos DB

Consulte os artigos sobre [cotas de serviço](concepts-limits.md) e [limites de taxa de transferência por contêiner e banco de dados](set-throughput.md#comparison-of-models) do Azure Cosmos DB para obter mais informações.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Perguntas frequentes sobre a API do SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como fazer para começar a desenvolver com a API do SQL?

Primeiro você deve se inscrever para uma assinatura do Azure. Depois de se inscrever em uma assinatura do Azure, você poderá adicionar o contêiner da API do SQL à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Azure Cosmos DB, consulte [Criar uma conta de banco de dados do Azure Cosmos](create-sql-api-dotnet.md#create-account).

[SDKs](sql-api-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs HTTP RESTful](/rest/api/cosmos-db/) para interagir com os recursos do Azure Cosmos DB em uma série de plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso acessar algumas amostras prontas para obter uma vantagem?

Exemplos para os SDKs do [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md) e [Python](sql-api-python-samples.md) da API do SQL estão disponíveis no GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>O banco de dados da API do SQL dá suporte para dados sem esquemas?

Sim, a API do SQL permite que os aplicativos armazenem documentos JSON arbitrários sem dicas nem definições de esquema. Os dados ficam disponíveis imediatamente para consulta por meio da interface de consulta SQL do Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>A API do SQL dá suporte para transações ACID?

Sim, a API do SQL dá suporte a transações entre documentos expressas como procedimentos armazenados e gatilhos do JavaScript. As transações têm como escopo uma única partição em cada contêiner e são executadas com a semântica ACID como “tudo ou nada”, isoladas de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida. 

### <a name="what-is-a-container"></a>O que é um contêiner?

Um contêiner é um grupo de documentos e de suas respectivas lógicas de aplicativo JavaScript associadas. Um contêiner é uma entidade faturável, em que o [custo](performance-levels.md) é determinado pela produtividade e pelo armazenamento usado. Os contêineres podem abranger um ou mais servidores ou partições e podem ser dimensionados para lidar com volumes de armazenamento ou de produtividade praticamente ilimitados.

* Para a API do SQL, o recurso é chamado de contêiner.
* Para contas da API do Cosmos DB para contas do MongoDB, o contêiner é mapeado para uma coleção.
* Para contas da API do Cassandra e de Tabela, o contêiner é mapeado para uma tabela.
* Para contas da API do Gremlin, o contêiner é mapeado para um grafo.

Os contêineres também são as entidades de cobrança do Azure Cosmos DB. Cada contêiner é cobrado por hora com base na produtividade provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte o [Preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Como crio um banco de dados?

Você pode criar bancos de dados usando o [Portal do Azure](https://portal.azure.com), conforme descrito em [Adicionar um contêiner](create-sql-api-java.md#add-a-container), um dos [SDKs do Azure Cosmos DB](sql-api-sdk-dotnet.md) ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como configuro usuários e permissões?

Você pode criar usuários e permissões usando um dos [SDKs da API do Cosmos DB](sql-api-sdk-dotnet.md) ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>A API do SQL dá suporta para SQL?

A linguagem de consulta SQL com suporte nas contas da API do SQL é um subconjunto avançado da funcionalidade de consulta com suporte no SQL Server. A linguagem de consulta SQL do Azure Cosmos DB fornece operadores hierárquicos e relacionais avançados e extensibilidade por meio de UDFs (funções definidas pelo usuário) baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados pelas técnicas de indexação automática do Azure Cosmos DB e pelo dialeto de consulta SQL do Azure Cosmos DB. Para obter informações sobre como usar a gramática SQL, consulte o artigo [Consulta SQL][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>A API do SQL dá suporte para funções de agregação SQL?

A API do SQL dá suporte à agregação de baixa latência em qualquer escala por meio das funções de agregação `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` pela gramática SQL. Para saber mais, consulte [Funções de agregação](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como a API do SQL fornece simultaneidade?

A API do SQL dá suporte ao OCC (controle de simultaneidade otimista) por meio de marcações da entidade HTTP ou Etags. Cada recurso da API do SQL tem uma Etag e a Etag é definida no servidor sempre que um documento é atualizado. O cabeçalho da Etag e o valor atual são incluídos em todas as mensagens de resposta. As Etags podem ser usadas com o cabeçalho If-Match a fim de permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-Match é o valor de Etag a ser comparado. Se o valor de Etag corresponder ao valor de Etag do servidor, o recurso será atualizado. Se a Etag não for mais atual, o servidor rejeitará a operação com um código de resposta "HTTP 412 Falha de pré-condição". O cliente refaz a busca do recurso a fim de obter o valor de Etag atual para o recurso. Além disso, as Etags podem ser usadas com o cabeçalho If-None-Match para determinar se é necessário realizar uma nova busca de um recurso.

Para usar a simultaneidade otimista em .NET, use a classe [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) . Para obter um exemplo de .NET, confira [Program.cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como fazer para realizar transações na API do SQL?

A API do SQL dá suporte a transações integradas à linguagem por meio de procedimentos armazenados e gatilhos do JavaScript. Todas as operações de banco de dados dentro de scripts são executadas em isolamento de instantâneo. Caso seja um contêiner de partição única, a execução terá o escopo definido para o contêiner. Se o contêiner estiver particionado, a execução terá o escopo definido para documentos com o mesmo valor de chave de partição dentro do contêiner. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Para obter mais informações, consulte a [Programação JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa no Cosmos DB?

Você pode fazer a inserção em massa documentos no Azure Cosmos DB de uma das seguintes maneiras:

* A ferramenta de executor em massa, conforme descrito em [Uso da biblioteca do .NET do executor em massa](bulk-executor-dot-net.md) e [Uso da biblioteca Java do executor em massa](bulk-executor-java.md)
* A ferramenta de migração de dados, conforme descrito em [Ferramenta de migração de banco de dados para o Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito em [Programação de JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>A API do SQL dá suporte ao cache de link de recursos?

Sim. Como o Azure Cosmos DB é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Os clientes da API do SQL podem especificar um cabeçalho “If-None-Match” para leituras em qualquer documento semelhante a recurso ou contêiner e atualizar suas cópias após a versão do servidor ser alterada.

### <a name="is-a-local-instance-of-sql-api-available"></a>Uma instância local da API do SQL está disponível?

Sim. O [Emulador do Azure Cosmos DB](local-emulator.md) fornece uma emulação de alta fidelidade do serviço Cosmos DB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o Emulador do Azure Cosmos DB e implanta-los no Azure em escala global fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que os valores de ponto flutuante longos em um documento são arredondados quando exibidos no Data Explorer no portal.

Esta é a limitação do JavaScript. O JavaScript usa números de formato de ponto flutuante de precisão dupla conforme especificado em IEEE 754 e pode manter, com segurança, apenas números entre -(2<sup>53</sup> - 1) e 2<sup>53</sup>-1 (isto é, 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Onde as permissões são permitidas na hierarquia do objeto?

A criação de permissões usando o ResourceTokens é permitida no nível do contêiner e de seus descendentes (como documentos, anexos). Isso implica que, no momento, tentar criar uma permissão no nível do banco de dados ou da conta não é permitido.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre perguntas frequentes de outras APIs, consulte:

* Perguntas frequentes sobre a [API do Azure Cosmos DB para MongoDB](mongodb-api-faq.md)
* Perguntas frequentes sobre a [API do Gremlin no Azure Cosmos DB](gremlin-api-faq.md)
* Perguntas frequentes sobre a [API do Cassandra no Azure Cosmos DB](cassandra-faq.md)
* Perguntas frequentes sobre a [API do Table no Azure Cosmos DB](table-api-faq.md)