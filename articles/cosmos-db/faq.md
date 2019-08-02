---
title: Perguntas frequentes sobre diferentes APIs no Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre o Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente. Aprenda sobre capacidade, níveis de desempenho e dimensionamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5cbbd507352471f5e093ef018de4b0c849aad442
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000788"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas frequentes sobre diferentes APIs no Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típicos do Azure Cosmos DB?

O Azure Cosmos DB é uma ótima escolha para novos aplicativos Web, móveis, de jogos e IoT, em que a escala automática, o desempenho previsível, os tempos de resposta rápidos na ordem de milissegundos e a capacidade de consultar dados sem esquemas são importantes. O Azure Cosmos DB funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como o Azure Cosmos DB oferece um desempenho previsível?

Uma RU ([Unidade de Solicitação](request-units.md)) é a medida de taxa de transferência no Azure Cosmos DB. Uma taxa de transferência de 1RU corresponde à taxa de transferência da obtenção de um documento de 1 KB. Toda operação no Azure Cosmos DB, incluindo leituras, gravações, consultas SQL e execuções de procedimentos armazenados, tem um valor determinístico de RU baseado na produtividade necessária para concluir a operação. Em vez de pensar em CPU, E/S, memória e como cada uma dessas medidas afeta a produtividade do seu aplicativo, você pode pensar em uma medida de RU única.

Você pode configurar cada contêiner Cosmos do Azure com taxa de transferência provisionada em termos de RUs de taxa de transferência por segundo. Em aplicativos de qualquer escala, você pode usar um parâmetro de comparação em solicitações individuais para medir seus valores de RU e provisionar um contêiner para manipular o total de unidades de solicitação em todas as solicitações. Também é possível aumentar ou reduzir verticalmente a produtividade de seu contêiner, conforme as necessidades do aplicativo mudam. Para obter mais informações sobre unidades de solicitação e ajuda para determinar suas necessidades de contêiner, experimente a [calculadora de produtividade](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como o Azure Cosmos DB dá suporte a vários modelos de dados, como chave/valor, colunar, documento e grafo?

Os modelos de dados chave/valor (tabela), colunar, documento e grafo têm suporte nativo devido ao design ARS (átomos, registros e sequências) no qual o Azure Cosmos DB é criado. Os átomos, os registros e as sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos já estão disponíveis (SQL, MongoDB, tabela e Gremlin) e outras específicas para modelos de dados adicionais estarão disponíveis no futuro.

O Azure Cosmos DB tem um mecanismo de indexação livre de esquemas, capaz de indexar automaticamente todos os dados que ingere sem a necessidade de qualquer esquema ou de índices secundários do desenvolvedor. O mecanismo apoia-se em um conjunto de layouts de índice lógico (invertido, colunar, árvore) que separam o layout de armazenamento do índice e dos subsistemas de processamento de consulta. O Cosmos DB também tem a capacidade de dar suporte a um conjunto de protocolos de transmissão e APIs de forma extensível e convertê-las com eficiência no modelo de dados principal (1) e nos layouts de índice lógico (2) tornando-o capaz de dar suporte a vários modelos de dados.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O Azure Cosmos DB está em conformidade com a HIPAA?

Sim, o Azure Cosmos DB está em conformidade com a HIPAA. A HIPAA estabelece os requisitos para o uso, a divulgação e a proteção de informações de integridade individualmente identificáveis. Para saber mais, confira o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

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

* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). O Stack Overflow é o melhor para perguntas sobre programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possíveis, tornando a pergunta clara e possível de responder](https://stackoverflow.com/help/how-to-ask).

Para solicitar novos recursos, crie uma nova solicitação em [Voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

Outras perguntas podem ser enviadas para a equipe em [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). No entanto, isso não é um alias de suporte técnico.

## <a id="try-cosmos-db"></a>Experimente as assinaturas do Azure Cosmos DB

Agora você pode aproveitar uma experiência do Azure Cosmos DB gratuitamente, por tempo limitado, sem assinatura e sem compromisso. Para se inscrever para uma assinatura de Experimente o Azure Cosmos DB gratuitamente, vá para [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Esta assinatura é separada da [Avaliação gratuita do Azure](https://azure.microsoft.com/free/) e pode ser usada juntamente com uma Avaliação gratuita do Azure ou uma assinatura paga do Azure.

Experimente as assinaturas do Azure Cosmos DB é exibido no portal do Azure ao lado de outras assinaturas associadas à sua ID de usuário.

As seguintes condições se aplicam às assinaturas de Experimente o Azure Cosmos DB:

* Um [contêiner de taxa de transferência provisionado](./set-throughput.md#set-throughput-on-a-container) por assinatura para SQL, API Gremlin e contas de tabela.
* Até três [coleções de taxa de transferência](./set-throughput.md#set-throughput-on-a-container) provisionada por assinatura para contas do MongoDB.
* Uma [taxa de transferência de banco de dados](./set-throughput.md#set-throughput-on-a-database) provisionada por assinatura. Os bancos de dados provisionados pela taxa de transferência podem conter qualquer número de contêineres dentro do.
* Capacidade de armazenamento de 10 GB.
* A replicação global está disponível atualmente nas seguintes [regiões do Azure](https://azure.microsoft.com/regions/): Centro dos EUA, Europa Setentrional e Sudeste Asiático
* Taxa de transferência máxima de 5 K RU/s quando provisionado no nível de contêiner.
* Taxa de transferência máxima de 20 K RU/s quando provisionado no nível do banco de dados.
* As assinaturas expiram após 30 dias e podem ser estendidas para um máximo de 31 dias.
* Não é possível criar tíquetes de suporte do Azure para contas Experimente o Azure Cosmos DB; no entanto, é oferecido suporte para assinantes com planos de suporte existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar o Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como fazer para se inscrever no Azure Cosmos DB?

O Azure Cosmos DB está disponível no Portal do Azure. Primeiro, inscreva-se para uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de Azure Cosmos DB à sua assinatura do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?

Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que têm a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestras. A chave mestra primária e a chave mestra secundária estão disponíveis na folha **chaves** da [portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões para as quais PreferredLocations pode ser definido?

O valor de PreferredLocations pode ser definido para qualquer uma das regiões do Azure em que o Cosmos DB está disponível. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há algo que devo saber ao distribuir dados pelo mundo todo por meio dos data centers do Azure?

O Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página [Regiões do Azure](https://azure.microsoft.com/regions/). Como ele é o serviço principal, cada novo data center tem uma presença do Azure Cosmos DB.

Quando você definir uma região, lembre-se de que o Azure Cosmos DB respeita nuvens soberanas e governamentais. Ou seja, se você criar uma conta em uma [região soberana](https://azure.microsoft.com/global-infrastructure/), não poderá replicar fora da [região soberana](https://azure.microsoft.com/global-infrastructure/). Da mesma forma, você não pode habilitar a replicação em outros locais soberanos de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível mudar do provisionamento de produtividade no nível do contêiner para o provisionamento de produtividade no nível do banco de dados? Ou vice-versa

O provisionamento de produtividade no nível do contêiner e no nível do banco de dados são ofertas separadas e mudar entre elas requer a migração de dados de origem para destino. Isso significa que você precisa criar um novo banco de dados ou uma nova coleção e, em seguida, migrar os dados usando a [biblioteca do executor em massa](bulk-executor-overview.md) ou o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>O Azure CosmosDB dá suporte à análise de série temporal?

Sim, o Azure CosmosDB dá suporte à análise de série temporal; veja um exemplo do [padrão de série temporal](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). O exemplo mostra como usar o feed de alterações para criar exibições agregadas nos dados de série temporal. Você pode estender essa abordagem usando o streaming do spark ou outro processador de dados de streaming.

## <a name="sql-api"></a>API do SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como fazer para começar a desenvolver com a API do SQL?

Primeiro você deve se inscrever para uma assinatura do Azure. Depois de se inscrever em uma assinatura do Azure, você poderá adicionar o contêiner da API do SQL à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Azure Cosmos DB, consulte [Criar uma conta de banco de dados do Azure Cosmos DB](create-sql-api-dotnet.md#create-account).

[SDKs](sql-api-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs HTTP RESTful](/rest/api/cosmos-db/) para interagir com os recursos do Azure Cosmos DB em uma série de plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso acessar algumas amostras prontas para obter uma vantagem?

Exemplos para os SDKs do [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md) e [Python](sql-api-python-samples.md) da API do SQL estão disponíveis no GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>O banco de dados da API do SQL dá suporte para dados sem esquemas?

Sim, a API do SQL permite que os aplicativos armazenem documentos JSON arbitrários sem dicas nem definições de esquema. Os dados ficam disponíveis imediatamente para consulta por meio da interface de consulta SQL do Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>A API do SQL dá suporte para transações ACID?

Sim, a API do SQL dá suporte a transações entre documentos expressas como procedimentos armazenados e gatilhos do JavaScript. As transações têm como escopo uma única partição em cada contêiner e são executadas com a semântica ACID como “tudo ou nada”, isoladas de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida. 

### <a name="what-is-a-container"></a>O que é um contêiner?

Um contêiner é um grupo de documentos e de suas respectivas lógicas de aplicativo JavaScript associadas. Um contêiner é uma entidade faturável, em que o [custo](performance-levels.md) é determinado pela produtividade e pelo armazenamento usado. Os contêineres podem abranger um ou mais servidores ou partições e podem ser dimensionados para lidar com volumes de armazenamento ou de produtividade praticamente ilimitados.

* Para contas da API do SQL e da API do Cosmos DB para contas do MongoDB, o contêiner é mapeado para uma coleção.
* Para contas da API do Cassandra e de Tabela, o contêiner é mapeado para uma tabela.
* Para contas da API do Gremlin, o contêiner é mapeado para um grafo.

Os contêineres também são as entidades de cobrança do Azure Cosmos DB. Cada contêiner é cobrado por hora com base na produtividade provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte o [Preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Como crio um banco de dados?

Você pode criar bancos de dados usando o [Portal do Azure](https://portal.azure.com), conforme descrito em [Adicionar uma coleção](create-sql-api-java.md#add-a-container), um dos [SDKs do Azure Cosmos DB](sql-api-sdk-dotnet.md) ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como configuro usuários e permissões?

Você pode criar usuários e permissões usando um dos [SDKs da API do Cosmos DB](sql-api-sdk-dotnet.md) ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>A API do SQL dá suporta para SQL?

A linguagem de consulta SQL com suporte nas contas da API do SQL é um subconjunto avançado da funcionalidade de consulta com suporte no SQL Server. A linguagem de consulta SQL do Azure Cosmos DB fornece operadores hierárquicos e relacionais avançados e extensibilidade por meio de UDFs (funções definidas pelo usuário) baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados pelas técnicas de indexação automática do Azure Cosmos DB e pelo dialeto de consulta SQL do Azure Cosmos DB. Para obter informações sobre como usar a gramática SQL, consulte o artigo [consulta SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>A API do SQL dá suporte para funções de agregação SQL?

A API do SQL dá suporte à agregação de baixa latência em qualquer escala por meio das funções de agregação `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` pela gramática SQL. Para saber mais, consulte [Funções de agregação](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como a API do SQL fornece simultaneidade?

A API do SQL dá suporte ao OCC (controle de simultaneidade otimista) por meio de marcações da entidade HTTP ou Etags. Cada recurso da API do SQL tem uma Etag e a Etag é definida no servidor sempre que um documento é atualizado. O cabeçalho da Etag e o valor atual são incluídos em todas as mensagens de resposta. As Etags podem ser usadas com o cabeçalho If-Match a fim de permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-Match é o valor de Etag a ser comparado. Se o valor de Etag corresponder ao valor de Etag do servidor, o recurso será atualizado. Se a Etag não for mais atual, o servidor rejeitará a operação com um código de resposta "HTTP 412 Falha de pré-condição". O cliente refaz a busca do recurso a fim de obter o valor de Etag atual para o recurso. Além disso, as Etags podem ser usadas com o cabeçalho If-None-Match para determinar se é necessário realizar uma nova busca de um recurso.

Para usar a simultaneidade otimista em .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para obter um exemplo de .NET, confira [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como fazer para realizar transações na API do SQL?

A API do SQL dá suporte a transações integradas à linguagem por meio de procedimentos armazenados e gatilhos do JavaScript. Todas as operações de banco de dados dentro de scripts são executadas em isolamento de instantâneo. Se ela for uma coleção de partição única, a execução terá o escopo definido para a coleção. Se a coleção estiver particionada, a execução terá o escopo definido para documentos com o mesmo valor de chave de partição dentro da coleção. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Para obter mais informações, consulte a [Programação JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa no Cosmos DB?

Você pode fazer a inserção em massa documentos no Azure Cosmos DB de uma das seguintes maneiras:

* A ferramenta de executor em massa, conforme descrito em [Uso da biblioteca do .NET do executor em massa](bulk-executor-dot-net.md) e [Uso da biblioteca Java do executor em massa](bulk-executor-java.md)
* A ferramenta de migração de dados, conforme descrito em [Ferramenta de migração de banco de dados para o Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito em [Programação de JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>A API do SQL dá suporte ao cache de link de recursos?

Sim. Como o Azure Cosmos DB é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Os clientes da API do SQL podem especificar um cabeçalho “If-None-Match” para leituras em qualquer documento semelhante a recurso ou coleção e atualizar suas cópias após a versão do servidor ser alterada.

### <a name="is-a-local-instance-of-sql-api-available"></a>Uma instância local da API do SQL está disponível?

Sim. O [Emulador do Azure Cosmos DB](local-emulator.md) fornece uma emulação de alta fidelidade do serviço Cosmos DB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o Emulador do Azure Cosmos DB e implanta-los no Azure em escala global fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que os valores de ponto flutuante longos em um documento são arredondados quando exibidos no Data Explorer no portal.

Esta é a limitação do JavaScript. O JavaScript usa números de formato de ponto flutuante de precisão dupla conforme especificado no IEEE 754 e pode conter com segurança números entre-(2<sup>53</sup> -1) e 2<sup>53</sup>-1 (ou seja, 9007199254740991) somente.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Onde as permissões são permitidas na hierarquia do objeto?

A criação de permissões usando o ResourceTokens é permitida no nível do contêiner e de seus descendentes (como documentos, anexos). Isso implica que, no momento, tentar criar uma permissão no nível do banco de dados ou da conta não é permitido.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>O que é a API do Azure Cosmos DB para MongoDB?

A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade de protocolo Wire que permite que os aplicativos se comuniquem de forma fácil e transparente com o mecanismo de banco de dados nativo do Azure Cosmos DB usando SDKs e drivers existentes com suporte da Comunidade para o MongoDB. Os desenvolvedores agora podem usar cadeias e habilidades existentes do MongoDB para criar aplicativos que tiram proveito do Azure Cosmos DB. Os desenvolvedores se beneficiam dos recursos exclusivos do Azure Cosmos DB, que incluem a distribuição global com replicação de vários mestres, indexação automática, manutenção de backup, SLAs (contratos de nível de serviço) com suporte financeiro etc.

### <a name="how-do-i-connect-to-my-database"></a>Como faço para me conectar com o meu banco de dados?

A maneira mais rápida de se conectar a um banco de dados do Cosmos com a API do Azure Cosmos DB para MongoDB é acessar o [portal do Azure](https://portal.azure.com). Vá para sua conta e, em seguida, no menu de navegação à esquerda, clique em **Início Rápido**. O início rápido é a melhor maneira de obter trechos de código para se conectar ao banco de dados.

O Azure Cosmos DB impõe padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por SSL, portanto, use o TLSv1.2.

Para saber mais, veja [Conectar-se ao banco de dados do Cosmos com a API do Azure Cosmos DB para MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Há outros códigos de erro adicionais com os quais eu preciso lidar enquanto estiver usando a API do Azure Cosmos DB para MongoDB?

Junto com os códigos de erro do MongoDB comuns, a API do Azure Cosmos DB para MongoDB tem seus próprios códigos de erro específicos:

| Erro               | Código  | DESCRIÇÃO  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere a possibilidade de dimensionar a taxa de transferência atribuída a um contêiner ou um conjunto de contêineres do portal do Azure ou tentar novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exemplo: <em> &nbsp; DB.&nbsp;GetCollection (' Users '). Aggregate ([ &nbsp; &nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {Name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {Age:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O driver Simba para MongoDB tem suporte para usar a API do Azure Cosmos DB para MongoDB?

Sim, você pode usar o driver ODBC Mongo do Simba com a API do Azure Cosmos DB para MongoDB

## <a id="table"></a>API de Tabela

### <a name="how-can-i-use-the-table-api-offering"></a>Como posso usar a oferta da API de Tabela?

O API de Tabela de Azure Cosmos DB está disponível no [portal do Azure][azure-portal]. Primeiro você deve se inscrever para uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de API de Tabela do Azure Cosmos DB à sua assinatura e, em seguida, adicionar tabelas à conta.

Você pode encontrar linguagens com suporte e inícios rápidos associados na [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>É necessário ter um novo SDK para usar a API de Tabela?

Não, SDKs de armazenamento existentes ainda devem funcionar. No entanto, recomenda-se que um sempre sejam obtidos os SDKs mais recentes para ter o melhor suporte e, em muitos casos, desempenho superior. Consulte a lista de linguagens disponíveis na [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Onde a API de Tabela não é idêntica com comportamento de armazenamento da Tabela do Azure?

Há algumas diferenças de comportamento de que os usuários provenientes do armazenamento de Tabela do Azure que desejam criar tabelas com a API de Tabela do Azure Cosmos DB devem estar cientes:

* A API de Tabela do Azure Cosmos DB usa um modelo de capacidade reservada para assegurar o desempenho garantido, mas isso significa que se paga pela capacidade assim que a tabela é criada, mesmo se a capacidade não estiver sendo usada. Com o armazenamento da Tabela do Azure, paga-se apenas pela capacidade que é usada. Isso ajuda a explicar por que a API de Tabela pode oferecer um SLA com leitura de 10 ms e gravação de 15 ms no 99º percentil enquanto o armazenamento de Tabela do Azure oferece um SLA de 10 segundos. Mas, como consequência, com tabelas da API de Tabela, até mesmo tabelas vazias sem quaisquer solicitações custam dinheiro para que se garanta que a capacidade esteja disponível para tratar de quaisquer solicitações feitas a elas no SLA oferecido pelo Azure Cosmos DB.
* Os resultados da consulta retornados pelo API de Tabela não são classificados em ordem de chave de partição/chave de linha, pois estão no armazenamento de tabelas do Azure.
* Chaves de linha podem ser de até 255 bytes apenas
* Os lotes só podem ter até 2 MBs
* Não há suporte para CORS no momento
* Os nomes de tabela no armazenamento de tabelas do Azure não diferenciam maiúsculas de minúsculas, mas estão em Azure Cosmos DB API de Tabela
* Alguns dos formatos internos do Azure Cosmos DB para a codificação de informações, como campos binários, atualmente não são tão eficientes quanto se desejaria. Portanto, isso pode causar limitações inesperadas no tamanho dos dados. Por exemplo, no momento você não poderia usar todo o 1 MB de uma entidade de tabela para armazenar dados binários, pois a codificação aumenta o tamanho dos dados.
* O nome da propriedade de entidade ' ID ' não tem suporte no momento
* TableQuery TakeCount não está limitado a 1000

Em termos da API REST, há várias opções de pontos de extremidade/consulta que não são suportados pela API de Tabela do Azure Cosmos DB:

| Métodos de REST | Opção de consulta/ponto de extremidade de REST | URLs de documento | Explicação |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Definir propriedades de serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) e [Obter propriedades do serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Esse ponto de extremidade é usado para definir as regras do CORS, a configuração de armazenamento da análise e as configurações de registro em log. Não há suporte para o CORS atualmente, e análises e registros em log são tratados de maneira diferente no Azure Cosmos DB em relação às Tabelas de Armazenamento do Azure |
| OPÇÕES | /\<table-resource-name> | [Solicitação de tabela preliminar de CORS ](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Isso faz parte do CORS para o qual o Azure Cosmos DB não dá suporte atualmente. |
| OBTER | /?restype=service@comp=stats | [Obter estatísticas do serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações sobre quão rapidamente os dados estão replicando entre primários e secundários. Isso não é necessária no Cosmos DB, uma vez que a replicação é parte de gravações. |
| GET, PUT | /mytable?comp=acl | [Obter ACL da tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) e [Definir ACL da tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Isso obtém e define as políticas de acesso armazenadas usadas para gerenciar assinaturas de acesso compartilhado (SAS). Embora haja suporte para SAS, elas são definidas e gerenciadas de modo diferente. |

Além disso, a API de Tabela do Azure Cosmos DB só dá suporte ao formato JSON, ao ATOM não.

Embora o Azure Cosmos DB dê suporte a assinaturas de acesso compartilhado (SAS), ele não dá suporte a determinadas políticas, especificamente as relacionadas a operações de gerenciamento, como o direito de criar novas tabelas.

Particularmente para o SDK do .NET em, há algumas classes e métodos para os quais Azure Cosmos DB não dá suporte atualmente.

| Classe | Método sem suporte |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (essa classe foi preterida) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Se qualquer uma dessas diferenças for um problema para o seu projeto, entre em contato com [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) e fale conosco.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como fazer para fornecer comentários sobre o SDK ou bugs?

Você pode compartilhar seus comentários em qualquer uma das seguintes maneiras:

* [Voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). O Stack Overflow é o melhor para perguntas sobre programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possíveis, tornando a pergunta clara e possível de responder](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de conexão que preciso usar para me conectar à API de Tabela?

A cadeia de conexão é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Você pode obter a cadeia de conexão na página Cadeia de Conexão no Portal do Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como substituo as definições de configuração das opções de solicitação no SDK do .NET para a API de Tabela?

Algumas configurações são tratadas no método CreateCloudTableClient e outras por meio do app.config na seção appSettings no aplicativo cliente. Para obter informações sobre definições de configuração, consulte [Recursos do Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Há alterações para clientes que estão usando SDKs de armazenamento existentes da Tabela do Azure?

nenhuma. Não há nenhuma alteração para clientes novos ou existentes que estejam usando os SDKs de armazenamento de Tabela do Azure existentes.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como fazer para exibir dados de tabela que são armazenados no Azure Cosmos DB para uso com a API de Tabela?

Use o portal do Azure para procurar os dados. Você também pode usar o código da API de Tabela ou as ferramentas mencionadas na próxima resposta.

### <a name="which-tools-work-with-the-table-api"></a>Quais ferramentas funcionam com a API de Tabela?

Você pode usar o [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Ferramentas que têm a flexibilidade para aceitar uma cadeia de conexão no formato especificado anteriormente podem dar suporte à nova API de Tabela. Uma lista das ferramentas de tabela é fornecida na página [Ferramentas de cliente do Armazenamento do Azure](../storage/common/storage-explorers.md).

### <a name="is-the-concurrency-on-operations-controlled"></a>A simultaneidade nas operações é controlada?

Sim, a simultaneidade otimista é fornecida com o uso do mecanismo de Etag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Há suporte para o modelo de consulta OData em entidades?

Sim, a API de Tabela dá suporte às consultas OData e LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso conectar ao Armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB lado a lado no mesmo aplicativo?

Sim, você pode se conectar criando duas instâncias separadas do CloudTableClient, cada uma apontando para seu próprio URI pela cadeia de conexão.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como fazer para migrar um aplicativo do armazenamento de Tabelas do Azure existente para essa oferta?

O [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e a [Ferramenta de migração de dados do Azure Cosmos DB](import-data.md) têm suporte.

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

A API de Tabela fornece a mesma funcionalidade de consulta que o armazenamento de Tabelas do Azure. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Para obter mais informações, consulte [Consultas SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando devo alterar a TableThroughput da API de Tabela?

Você deve alterar TableThroughput quando uma das seguintes condições se aplicar:

* Você estiver executando uma ETL (extração, transformação e carregamento) de dados ou desejar carregar muitos dados em um curto período de tempo.
* Você precisa de mais taxa de transferência do contêiner ou de um conjunto de contêineres no back-end. Por exemplo, você verá que a produtividade usada é maior do que a provisionada e você está sendo limitado. Para obter mais informações, consulte [Definir a taxa de transferência para contêineres do Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Posso escalar verticalmente ou reduzir verticalmente a produtividade da minha tabela da API de Tabela (versão prévia)?

Sim, você pode usar o painel de escala do portal do Azure Cosmos DB para dimensionar a produtividade. Para obter mais informações, veja [Configurar produtividade](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Há uma configuração padrão de TableThroughput para as tabelas recém-provisionadas?

Sim, se você não substituir a TableThroughput por meio do app.config e não usar um contêiner pré-criado no Azure Cosmos DB, o serviço criará uma Tabela com uma produtividade de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Há alguma alteração de preço para clientes atuais do serviço de armazenamento de Tabela do Azure?

nenhuma. Não há nenhuma alteração no preço para clientes de armazenamento de Tabela do Azure existentes.

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

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Para assegurar o desempenho premium garantido, certifique-se de que seu índice e tamanho de dados sejam gerenciáveis e escalonáveis. O limite de 10 GB no número de entidades ou itens por chave de partição serve para garantir que fornecemos um excelente desempenho de pesquisa e consulta. Para garantir que seu aplicativo dimensiona bem, mesmo para o Armazenamento do Microsoft Azure, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey e RowKey ainda são necessárias na API de Tabela?

Sim. Uma vez que a área de superfície da API de Tabela é semelhante à do SDK de armazenamento de Tabelas do Azure, a chave de partição oferece uma maneira eficiente de distribuir os dados. A chave de linha é exclusiva nessa partição. A chave de linha deve estar presente e não pode ser nula como no SDK Standard. O comprimento da RowKey é de 255 bytes e da PartitionKey é de 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro da API de Tabela?

O armazenamento da Tabela do Azure e da API de Tabela do Azure Cosmos DB usam os mesmos SDKs, então a maioria dos erros serão os mesmos.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que recebo um limite quando tento criar várias tabelas uma após a outra na API de Tabela?

O Azure Cosmos DB é um sistema baseado em SLA que fornece garantia de latência, produtividade, disponibilidade e consistência. Como ele é um sistema provisionado, ele reserva recursos para garantir esses requisitos. A rápida taxa de criação de tabelas é detectada e limitada. É recomendável que você examine a taxa de criação de tabelas e a reduza para menos de cinco por minuto. Lembre-se de que a API de Tabela é um sistema provisionado. No momento em que você a provisionar, começará a pagar por ela.

## <a name="gremlin-api"></a>API do Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para o desenvolvimento em C#/.NET, posso usar o pacote Microsoft.Azure.Graphs ou o Gremlin.NET?

A API do Gremlin do Azure Cosmos DB aproveita os drivers de fonte aberta como os principais conectores do serviço. Portanto, a opção recomendada é usar os [drivers compatíveis com o Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como as RU/s são cobradas ao executar consultas em um banco de dados do gráfico?

Todos os objetos do grafo, vértices e bordas são mostrados como documentos JSON no back-end. Já que uma consulta Gremlin pode modificar um ou muitos objetos do gráfico por vez, o custo associado a ela está diretamente relacionado aos objetos, que são bordas processadas pela consulta. É o mesmo processo que o Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, consulte [Unidades de Solicitação no Azure Cosmos DB](request-units.md).

O custo da RU é baseado no conjunto de dados de trabalho da passagem, não no conjunto de resultados. Por exemplo, se uma consulta tem por objetivo obter um único vértice como resultado, mas precisa atravessar vários outros objetos no caminho, então, o custo se baseará em todos os objetos do gráfico necessários para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que um banco de dados de gráfico pode ter na API do Gremlin do Azure Cosmos DB?

O Azure Cosmos DB utiliza o [particionamento horizontal](partition-data.md) para aumentar automaticamente o endereço nos requisitos de armazenamento e da taxa de transferência. A capacidade de armazenamento e da taxa de transferência máxima de uma carga de trabalho é determinada pelo número de partições associadas a uma determinada coleção. No entanto, uma coleção de APIs do Gremlin tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada em grande escala. Para obter mais informações sobre particionamento e práticas recomendadas, consulte o [particionamento no artigo do Azure Cosmos DB](partition-data.md).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como me proteger contra ataques de injeção usando os drivers do Gremlin?

A maioria dos drivers Tinkerpop Gremlin do Apache nativos permitem a opção de fornecer um dicionário de parâmetros para a execução da consulta. Este é um exemplo de como fazer isso no [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e no [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Por que estou vendo o "Erro de Compilação da Consulta Gremlin: não é possível encontrar nenhum método"?

A API do Gremlin do Azure Cosmos DB implementa um subconjunto da funcionalidade definido na área de superfície do Gremlin. Para obter etapas com suporte e mais informações, confira o artigo [Suporte do Gremlin](gremlin-support.md).

A melhor solução é regravar as etapas necessárias do Gremlin com a funcionalidade compatível, pois todas as etapas essenciais do Gremlin são compatíveis com o Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Por que estou vendo o erro "WebSocketException: o servidor retornou o código de status '200' quando o código de status '101' era esperado"?

Esse erro provavelmente é gerado quando o ponto de extremidade incorreto é usado. O ponto de extremidade que gera esse erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto de extremidade do documento para o banco de dados do gráfico.  O correto a usar é o Ponto de Extremidade do Gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Por que estou vendo o erro "RequestRateIsTooLarge"?

Esse erro significa que as Unidades de Solicitação alocadas por segundo não são suficientes para atender a consulta. O erro geralmente é visto quando você executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta tentará recuperar todos os vértices do gráfico. Portanto, o custo será igual a, pelo menos, o número de vértices em termos de RUs. A configuração de RU/s deve ser ajustada para atender a essa consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Por que minhas conexões de driver do Gremlin são descartadas com o tempo?

Uma conexão do Gremlin é feita por meio de uma conexão WebSocket. Embora as conexões WebSocket não tenham um tempo específico de duração, a API do Gremlin do Azure Cosmos DB encerrará as conexões ociosas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não é possível usar chamadas da API fluentes nos drivers do Gremlin nativos?

As chamadas da API fluentes ainda não têm suporte da API do Gremlin do Azure Cosmos DB. Chamadas à API fluentes exigem um recurso de formatação interno conhecido como suporte do código de bytes que atualmente não é compatível com a API do Gremlin do Azure Cosmos DB. Devido à mesma razão, o driver do JavaScript mais recente do Gremlin também não é suportado atualmente.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Como posso avaliar a eficiência das minhas consultas Gremlin?

A etapa de versão prévia do **executionProfile()** pode ser usada para fornecer uma análise do plano de execução de consulta. Essa etapa precisa ser adicionada ao final de qualquer consulta Gremlin, como ilustrado no exemplo a seguir:

**Exemplo de consulta**

```
g.V('mary').out('knows').executionProfile()
```

**Saída de exemplo**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A saída do perfil acima mostra quanto tempo é gasto para obter os objetos de vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isso está relacionado às medidas de custo padrão para as consultas do Azure Cosmos DB.

## <a id="cassandra"></a> API do Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual é a versão de protocolo com suporte pelo Azure Cosmso API do Cassandra DB? Existe um plano para oferecer suporte a outros protocolos?

A API do Apache Cassandra para o Azure Cosmos DB hoje tem suporte para a versão 4 do CQL. Se você tiver comentários sobre o suporte a outros protocolos, conte-nos por meio dos [comentários da voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por que é necessário escolher uma taxa de transferência para uma tabela?

O Azure Cosmos DB define a taxa de transferência padrão para seu contêiner com base em onde você cria a tabela - portal ou CQL.
O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Configurar a taxa de transferência assegura que você obtenha taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.
Você pode alterar a taxa de transferência de forma flexível para se beneficiar da sazonalidade de seu aplicativo e reduzir custos.

O conceito de taxa de transferência é explicado no artigo [Unidades de solicitação no Azure Cosmos DB](request-units.md). A taxa de transferência para uma tabela é distribuída igualmente entre as partições físicas subjacentes.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual é a RU/s padrão de uma tabela criada por meio de CQL? E se eu precisar alterá-la?

O Azure Cosmos DB usa unidades de solicitação por segundo (RU/s) como moeda para fornecer a taxa de transferência. As tabelas criadas por meio de CQL têm 400 RU. Você pode alterar a RU do portal.

CQL

```
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

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Isso é possível ao configurar a taxa de transferência, o que assegura que você obterá taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.
Ao exceder essa capacidade, você recebe uma mensagem de erro de sobrecarregamento indicando que sua capacidade foi usada.
0x1001 Sobrecarregado: a solicitação não pode ser processada porque "A taxa de solicitação é grande". Nesse momento, é essencial ver quais operações e respectivos volumes causam esse problema. Você pode ter uma ideia da capacidade consumida que excede a capacidade provisionada com a métrica no portal. Em seguida, você precisa garantir que a capacidade seja consumida de forma quase igual entre todas as partições subjacentes. Se você vir que a maior parte da taxa de transferência é consumida por uma partição, significa que há distorção de carga de trabalho.

Há uma métrica disponível para mostrar como a taxa de transferência é usada ao longo de horas, dias e por sete dias, nas partições ou no todo. Para obter mais informações, consulte [Como monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md).

Logs de diagnóstico são explicados no artigo [Logs de diagnóstico do Azure Cosmos DB](logging.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>A chave primária mapeia para o conceito de chave de partição do Azure Cosmos DB?

Sim, a chave de partição é usada para colocar a entidade no local correto. No Azure Cosmos DB, ela é usada para localizar a partição lógica correta que é armazenada em uma partição física. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partition-data.md). A questão essencial aqui é que uma partição lógica não deve exceder o limite de 10 GB hoje.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação de “cota cheia” indicando que uma partição está cheia?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Esse armazenamento ilimitado baseia-se na escala horizontal de dados usando o particionamento como o conceito chave. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partition-data.md).

Você deve respeitar o limite de 10 GB no número de entidades ou itens por partição lógica. Para garantir que seu aplicativo dimensiona bem, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a. Esse erro poderá apenas surgir se os dados forem distorcidos: ou seja, muitos dados para uma chave de partição (mais do que 10&nbsp;GB). Você pode localizar a distribuição de dados usando o portal de armazenamento. Uma maneira de corrigir esse erro é recriando a tabela e escolhendo um primário granular (chave de partição), que permita uma melhor distribuição de dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível usar a API do Cassandra como armazenamento de valor de chave com milhões ou bilhões de chaves de partições individuais?

O Azure Cosmos DB pode armazenar dados ilimitados expandindo o armazenamento. Isso é independente da taxa de transferência. Sim, sempre há a opção de usar a API do Cassandra para armazenar e recuperar  chave/valores especificando a chave primária/de partição correta. Essas chaves individuais possuem sua própria partição lógica e permanecem acima da partição física sem problemas.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar mais de uma tabela com a API do Apache Cassandra do Azure Cosmos DB?

Sim, é possível criar mais de uma tabela com a API do Apache Cassandra. Cada uma dessas tabelas é tratada como unidade de taxa de transferência e armazenamento.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>É possível criar mais de uma tabela em sucessão?

O Azure Cosmos DB é um sistema de recursos administrado para atividades de plano de dados e controle. Contêineres como coleções e tabelas são entidades de tempo de execução provisionadas para uma determinada capacidade de taxa de transferência. A criação desses contêineres em sucessão rápida não é uma atividade esperada e limitada. Se você tiver testes que removem/criam tabelas imediatamente, tente espaçá-los.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Qual é a quantidade máxima de tabelas que pode ser criada?

Não há nenhum limite físico no número de tabelas, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se você tiver um número grande de tabelas (no qual o tamanho constante total excede 10 TB de dados) que precisem ser criadas nos usuais 10 ou 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Qual é o número máximo de keyspace que podemos criar?

Não há limite físico no número de keyspaces por serem contêineres de metadados, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se você tiver um número grande de keyspaces por alguma razão.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível colocar em uma grande quantidade de dados depois de iniciar de uma tabela normal?

A capacidade de armazenamento é gerenciada automaticamente e aumenta à medida que mais dados forem enviados por push. Portanto, você pode importar com confiança a quantidade de dados que precisar sem o gerenciamento e provisionamento de nós e muito mais.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer configurações de arquivo yaml para configurar o comportamento da API Casssandra do Apache do Azure Cosmos DB?

A API do Apache Cassandra do Azure Cosmos DB é um serviço de plataforma. Ela fornece compatibilidade em nível de protocolo para a execução de operações. Ela oculta a complexidade de gerenciamento, monitoramento e configuração. Como desenvolvedor/usuário, você não precisa se preocupar quanto à disponibilidade, marcas de exclusão, cache de chaves, cache de linhas, filtro de bloom e uma variedade de outras configurações. A API do Apache Cassandra do Azure Cosmos DB se concentra em fornecer o desempenho de leitura e gravação de que você precisa sem a sobrecarga de configuração e gerenciamento.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>A API do Apache Cassandra do Azure Cosmos DB oferece suporte a comandos de adição de nós/cluster de nós/status de nós?

A API do Apache Cassandra é um serviço de plataforma que facilita o planejamento de capacidade, a resposta às demandas de elasticidade para taxa de transferência e o armazenamento. Com o Azure Cosmos DB, você provisiona a taxa de transferência de que precisa. Depois é possível escalá-la para cima e para baixo quantas vezes quiser por dia sem se preocupar com adição/exclusão de nós ou em gerenciá-los. Isso implica que você não precisa usar o nó, nem a ferramenta de gerenciamento de cluster.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece em relação a várias definições de configuração para a criação de keyspaces como simples/rede?

O Azure Cosmos DB fornece distribuição global imediata por motivos de baixa latência e disponibilidade. Você não precisa instalar réplicas ou outras coisas. Todas as gravações são sempre o quorum permanentemente confirmado em qualquer região em que você escreve enquanto fornece garantias de desempenho.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>O que acontece em relação a várias configurações para metadados de tabela, como filtro de bloom, cache, alteração de reparo de leitura, gc_grace, compactação memtable_flush_period, etc.?

O Azure Cosmos DB fornece desempenho para leituras/gravações e taxa de transferência sem a necessidade de mexer nas definições de configuração e acidentalmente manipulá-las.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Há suporte para o tempo de vida (TTL) para tabelas Cassandra?

Sim, há suporte para TTL.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorar status do nó, status de réplica, gc e parâmetros de sistema operacional mais cedo com diversas ferramentas? O que precisa ser monitorado agora?

O Azure Cosmos DB é um serviço de plataforma que ajuda a aumentar a produtividade e não se preocupar com o gerenciamento e monitoramento da infraestrutura. Você precisa cuidar da taxa de transferência que está disponível na métrica do portal para saber se está sendo restringido e aumentar ou diminuir essa taxa de transferência.
[SLAs](monitor-accounts.md) do monitor.
Usar [Métrica](use-metrics.md) Usar [Logs de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Quais SDKs de cliente podem funcionar com a API do Apache Cassandra do Azure Cosmos DB?

Os drivers de cliente do Apache Cassandra SDK que usam CQLv3 foram usados para programas cliente. Se houver outros drivers que você use ou se você estiver enfrentando problemas, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Há suporte para chave de partição de composição?

Sim, é possível usar a sintaxe regular para criar uma chave de partição de composição.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar sstableloader para carregamento de dados?

Não, não há suporte para sstableloader.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Um cluster apache Cassandra local pode ser emparelhado com API do Cassandra do Azure Cosmos DB?

No momento, o Azure Cosmos DB tem uma experiência otimizada para o ambiente em nuvem sem a sobrecarga de operações. Se você precisar de emparelhamento, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos trabalhando na oferta para ajudar a emparelhar o cluster Cassandra de nuvem local/diferente para o API do Cassandra do Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>A API do Cassandra fornece backups completos?

O Azure Cosmos DB hoje fornece dois backups completos gratuitos realizados em intervalos de quatro horas em todas as APIs. Isso garante que você não precise configurar uma agenda de backup e outras coisas.
Se você quiser modificar a retenção e a frequência, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ou abra um caso de suporte. As informações sobre a funcionalidade de backup são fornecidas no artigo [Backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como uma conta da API Cassandra lida com o failover caso uma região fique inativa?

A API Cassandra do Azure Cosmos DB pega emprestado da plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o tempo de inatividade do data center, habilite pelo menos mais uma região para a conta no Portal do Azure Cosmos DB [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md). Defina a prioridade da região usando o portal [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md).

Você pode adicionar quantas regiões quiser para a conta e controlar para onde ela pode fazer o failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo lá também. Quando você fizer isso, os clientes não terão o tempo de inatividade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>A API Cassandra indexa todos os atributos de uma entidade por padrão?

API do Cassandra está planejando dar suporte à indexação secundária para ajudar a criar um índice seletivo em determinados atributos. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK da API Cassandra localmente com o emulador?

Sim, há suporte para isso.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB como uma plataforma parece ter muitos recursos, como o feed de alterações e outras funcionalidades. Esses recursos serão adicionados à API do Cassandra?

A API do Apache Cassandra fornece a mesma funcionalidade CQL do Apache Cassandra. Planejamos examinar a possibilidade de oferecer suporte a vários recursos no futuro.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Recurso X da API do Apache Cassandra comum não está funcionando hoje, onde posso deixar comentários a respeito?

Deixe comentários por meio dos [comentários da voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
