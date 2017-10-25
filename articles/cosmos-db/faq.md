---
title: Perguntas frequentes sobre o Azure Cosmos DB| Microsoft Docs
description: "Obtenha respostas para perguntas frequentes sobre o Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente. Aprenda sobre capacidade, níveis de desempenho e dimensionamento."
keywords: Perguntas de banco de dados, perguntas frequentes, documentdb, azure, Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: mimig
ms.openlocfilehash: e8ac4bef5af7a98857278b82304bb58f1ac6e342
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-faq"></a>Perguntas frequentes do Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Conceitos básicos do Azure Cosmos DB
### <a name="what-is-azure-cosmos-db"></a>O que é o Azure Cosmos DB?
O Azure Cosmos DB é um serviço de multimodelo de banco de dados replicado globalmente que oferece consultas avançadas de dados sem esquemas, ajuda a fornecer um desempenho confiável e configurável e possibilita um desenvolvimento rápido. Isso é obtida por meio de uma plataforma gerenciada apoiada pela potência e alcance do Microsoft Azure. 

O Azure Cosmos DB é a solução ideal para aplicativos Web, móveis, de jogos e IoT quando a produtividade previsível, alta disponibilidade, baixa latência e o modelo de dados sem esquemas são requisitos fundamentais. Ele fornece flexibilidade de esquemas e indexação avançada e inclui suporte a transações de vários documentos com JavaScript integrado. 

Para ver mais perguntas e respostas sobre banco de dados, além de instruções sobre como implantar e usar esse serviço, consulte a [página de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>O que aconteceu com o DocumentDB?
A API do DocumentDB é uma das APIs e dos modelos de dados com suporte no Azure Cosmos DB. Além disso, o Azure Cosmos DB dá suporte com a API do Graph (versão prévia), a API de Tabela (versão prévia) e a API do MongoDB. Para obter mais informações, consulte [Perguntas de clientes do DocumentDB](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Como fazer para acessar minha conta do DocumentDB no portal do Azure?
No Portal do Azure, clique no ícone do Azure Cosmos DB no painel esquerdo. Se você já tinha uma conta do DocumentDB, agora você tem uma conta do Azure Cosmos DB, sem alterações na cobrança.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típicos do Azure Cosmos DB?
O Azure Cosmos DB é uma ótima escolha para novos aplicativos Web, móveis, de jogos e IoT, em que a escala automática, o desempenho previsível, os tempos de resposta rápidos na ordem de milissegundos e a capacidade de consultar dados sem esquemas são importantes. O Azure Cosmos DB funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como o Azure Cosmos DB oferece um desempenho previsível?
Uma RU ([Unidade de Solicitação](request-units.md)) é a medida de taxa de transferência no Azure Cosmos DB. Uma produtividade de 1 RU corresponde à produtividade do GET de um documento de 1 KB. Toda operação no Azure Cosmos DB, incluindo leituras, gravações, consultas SQL e execuções de procedimentos armazenados, tem um valor determinístico de RU baseado na produtividade necessária para concluir a operação. Em vez de pensar em CPU, E/S, memória e como cada uma dessas medidas afeta a produtividade do seu aplicativo, você pode pensar em uma medida de RU única.

Você pode reservar cada contêiner do Azure Cosmos DB com produtividade provisionada em termos de RUs da produtividade por segundo. Em aplicativos de qualquer escala, você pode usar um parâmetro de comparação em solicitações individuais para medir seus valores de RU e provisionar um contêiner para manipular o total de unidades de solicitação em todas as solicitações. Também é possível aumentar ou reduzir verticalmente a produtividade de seu contêiner, conforme as necessidades do aplicativo mudam. Para obter mais informações sobre unidades de solicitação, bem como ajuda para determinar as necessidades do contêiner, veja [Estimativa das necessidades de produção](request-units.md#estimating-throughput-needs) e experimente a [calculadora de produtividade](https://www.documentdb.com/capacityplanner). O termo *contêiner* refere-se aqui a uma coleção de API do DocumentDB, um gráfico da API do Graph, uma coleção da API do MongoDB e uma tabela da API de Tabela. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como o Azure Cosmos DB dá suporte a vários modelos de dados, como chave/valor, colunar, documento e gráfico?

Os modelos de dados chave/valor (tabela), colunar, documento e gráfico têm suporte nativo devido ao design ARS (átomos, registros e sequências) no qual o Azure Cosmos DB é criado. Os átomos, os registros e as sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos já estão disponíveis (DocumentDB, MongoDB, Tabela e APIs do Graph) e as demais, específicas para outros modelos de dados, estarão disponíveis no futuro.

O Azure Cosmos DB tem um mecanismo de indexação livre de esquemas, capaz de indexar automaticamente todos os dados que ingere sem a necessidade de qualquer esquema ou de índices secundários do desenvolvedor. O mecanismo apoia-se em um conjunto de layouts de índice lógico (invertido, colunar, árvore) que separam o layout de armazenamento do índice e dos subsistemas de processamento de consulta. O Cosmos DB também tem a capacidade de dar suporte a um conjunto de protocolos de transmissão e APIs de forma extensível e convertê-las com eficiência no modelo de dados principal (1) e nos layouts de índice lógico (2) tornando-o capaz de dar suporte nativo a vários modelos de dados.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O Azure Cosmos DB está em conformidade com a HIPAA?
Sim, o Azure Cosmos DB está em conformidade com a HIPAA. A HIPAA estabelece os requisitos para o uso, a divulgação e a proteção de informações de integridade individualmente identificáveis. Para saber mais, confira o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento do Azure Cosmos DB?
Não há limite para a quantidade total de dados que um contêiner pode armazenar no Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de produtividade do Azure Cosmos DB?
Não há limite para a quantidade total de produtividade para a qual um contêiner dá suporte no Azure Cosmos DB. A ideia principal é distribuir sua carga de trabalho aproximadamente de forma uniforme entre um número suficientemente grande de chaves de partição.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa o Azure Cosmos DB?
Para obter detalhes, consulte a página [Detalhes de preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Os encargos pelo uso do Azure Cosmos DB são determinados pelo número de contêineres provisionados, pelo número de horas em que os contêineres estavam online e pela produtividade provisionada de cada contêiner. O termo *contêineres* refere-se aqui à coleção de API do DocumentDB, ao gráfico da API do Graph, à coleção da API do MongoDB e às tabelas da API de Tabela. 

### <a name="is-a-free-account-available"></a>Existe uma conta gratuita disponível?
Sim, você pode se inscrever para uma conta de tempo limitado sem custo adicional, sem nenhum compromisso. Para se inscrever, visite [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) ou leia mais em [Tente as Perguntas frequentes do Azure Cosmos DB](#try-cosmos-db).

Se você for novo no Azure, poderá se inscrever em uma [conta gratuita do Azure](https://azure.microsoft.com/free/), que dá 30 dias e um crédito para experimentar todos os serviços do Azure. Se você tem uma assinatura do Visual Studio, também está qualificado para receber [créditos Azure gratuitos](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usar em qualquer serviço do Azure. 

Também use o [Emulador do Azure Cosmos DB](local-emulator.md) para desenvolver e testar seu aplicativo no local, gratuitamente, sem criar uma assinatura do Azure. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador do Azure Cosmos DB, você poderá passar a usar uma conta do Azure Cosmos DB na nuvem.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como fazer para obter mais ajuda com o Azure Cosmos DB?
Caso precise de ajuda, contate-nos no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) ou no [fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) ou agende um chat individual com a equipe de engenharia do Azure Cosmos DB enviando um email para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Experimente as assinaturas do Azure Cosmos DB

Agora você pode aproveitar uma experiência do Azure Cosmos DB gratuitamente, por tempo limitado, sem assinatura e sem compromisso. Para se inscrever para uma assinatura de Experimente o Azure Cosmos DB gratuitamente, vá para [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Esta assinatura é separada do [Avaliação gratuita do Azure](https://azure.microsoft.com/free/) e pode ser usada juntamente com uma Avaliação gratuita do Azure ou uma assinatura paga do Azure. 

Experimente as assinaturas do Azure Cosmos DB é exibido no portal do Azure ao lado de outras assinaturas associadas à sua ID de usuário. 

As seguintes condições se aplicam às assinaturas de Experimente o Azure Cosmos DB:

* Um contêiner por assinatura para SQL (API do DocumentDB), Gremlin (API do Graph) e contas de Tabela.
* Até 3 coleções por assinatura para contas do MongoDB.
* Capacidade de armazenamento de 10 GB.
* A replicação global está disponível nas seguintes [regiões do Azure](https://azure.microsoft.com/regions/): Centro dos EUA, Norte da Europa e Sudeste Asiático
* Produtividade máxima de 5K RU/s.
* As assinaturas expiram após 24 horas e podem ser estendidas para um máximo de 48 horas no total.
* Não é possível criar tíquetes de suporte do Azure para contas Experimente o Azure Cosmos DB; no entanto, é oferecido suporte para assinantes com planos de suporte existentes. 

## <a name="set-up-azure-cosmos-db"></a>Configurar o Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como fazer para se inscrever no Azure Cosmos DB?
O Azure Cosmos DB está disponível no Portal do Azure. Primeiro, inscreva-se para uma assinatura do Azure. Depois de se inscrever, você poderá adicionar uma conta da API do DocumentDB, da API do Graph (versão prévia), da API de Tabela (versão prévia) ou da API do MongoDB à sua assinatura do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?
Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que têm a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestras. A chave mestra primária e a secundária estão disponíveis na folha **Chaves** do [Portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões para as quais PreferredLocations pode ser definido? 
O valor de PreferredLocations pode ser definido para qualquer uma das regiões do Azure em que o Cosmos DB está disponível. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há algo que devo saber ao distribuir dados pelo mundo todo por meio dos data centers do Azure? 
O Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página [Regiões do Azure](https://azure.microsoft.com/regions/). Como ele é o serviço principal, cada novo data center tem uma presença do Azure Cosmos DB. 

Quando você definir uma região, lembre-se de que o Azure Cosmos DB respeita nuvens soberanas e governamentais. Ou seja, se você criar uma conta em uma região soberana, não poderá replicar fora da região soberana. Da mesma forma, você não pode habilitar a replicação em outros locais soberanos de uma conta externa. 

## <a name="develop-against-the-documentdb-api"></a>Desenvolver com o API do DocumentDB

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Como fazer para desenvolver na API do DocumentDB?
A API do Microsoft DocumentDB está disponível no [Portal do Azure][azure-portal]. Primeiro você deve se inscrever para uma assinatura do Azure. Depois de se inscrever em uma assinatura do Azure, você poderá adicionar o contêiner da API do DocumentDB à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Azure Cosmos DB, consulte [Criar uma conta de banco de dados do Azure Cosmos DB](create-documentdb-dotnet.md#create-account). Se você já tinha uma conta do DocumentDB, agora terá uma conta do Azure Cosmos DB. 

[SDKs](documentdb-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs HTTP RESTful](/rest/api/documentdb/) para interagir com os recursos do Azure Cosmos DB em uma série de plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso acessar algumas amostras prontas para obter uma vantagem?
Amostras dos SDKs do [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) e [Python](documentdb-python-samples.md) da API do DocumentDB estão disponíveis no GitHub.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>O banco de dados da API do DocumentDB dá suporte a dados sem esquemas?
Sim, a API do DocumentDB permite que os aplicativos armazenem documentos JSON arbitrários sem dicas nem definições de esquema. Os dados ficam disponíveis imediatamente para consulta por meio da interface de consulta SQL do Azure Cosmos DB.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>A API do DocumentDB dá suporte a transações ACID?
Sim, a API do DocumentDB dá suporte a transações entre documentos expressas como procedimentos armazenados e gatilhos do JavaScript. As transações têm como escopo uma única partição em cada coleção e são executadas com semântica ACID como “tudo ou nada”, isoladas de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida. Para saber mais sobre transações, confira [Transações de programa de banco de dados](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>O que é uma coleção?
Uma coleção é um grupo de documentos e sua lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](performance-levels.md) é determinado pela produtividade e pelo armazenamento usado. As coleções podem abranger uma ou mais partições ou servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados.

As coleções também são as entidades de cobrança do Azure Cosmos DB. Cada coleção é cobrada por hora com base na produtividade provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte o [Preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Como crio um banco de dados?
Você pode criar bancos de dados usando o [Portal do Azure](https://portal.azure.com), conforme descrito em [Adicionar uma coleção](create-documentdb-dotnet.md#create-collection), um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) ou as [APIs REST](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Como configuro usuários e permissões?
Você pode criar usuários e permissões usando um dos [SDKs da API do Cosmos DB](documentdb-sdk-dotnet.md) ou as [APIs REST](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>A API do DocumentDB dá suporte ao SQL?
A linguagem de consulta SQL é um subconjunto avançado da funcionalidade de consulta com suporte no SQL. A linguagem de consulta SQL do Azure Cosmos DB fornece operadores hierárquicos e relacionais avançados e extensibilidade por meio de UDFs (funções definidas pelo usuário) baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados pelas técnicas de indexação automática do Azure Cosmos DB e pelo dialeto de consulta SQL do Azure Cosmos DB. Para obter informações sobre o uso da gramática SQL, consulte o artigo [QueryDocumentDB][query].

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>A API do DocumentDB dá suporte às funções de agregação SQL?
A API do DocumentDB dá suporte à agregação de baixa latência em qualquer escala por meio das funções de agregação `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` pela gramática SQL. Para saber mais, consulte [Funções de agregação](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Como a API do DocumentDB fornece simultaneidade?
A API do DocumentDB dá suporte ao OCC (controle de simultaneidade otimista) por meio de marcas da entidade HTTP ou Etags. Cada recurso da API do DocumentDB tem uma Etag e a Etag é definida no servidor sempre que um documento é atualizado. O cabeçalho da Etag e o valor atual são incluídos em todas as mensagens de resposta. As Etags podem ser usadas com o cabeçalho If-Match a fim de permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-Match é o valor de Etag a ser comparado. Se o valor de Etag corresponder ao valor de Etag do servidor, o recurso será atualizado. Se a Etag não for mais atual, o servidor rejeitará a operação com um código de resposta "HTTP 412 Falha de pré-condição". O cliente refaz a busca do recurso a fim de obter o valor de Etag atual para o recurso. Além disso, as Etags podem ser usadas com o cabeçalho If-None-Match para determinar se é necessário realizar uma nova busca de um recurso.

Para usar a simultaneidade otimista em .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para obter um exemplo de .NET, confira [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Como fazer para executar transações na API do DocumentDB?
A API do DocumentDB dá suporte a transações integradas à linguagem por meio de procedimentos armazenados e gatilhos do JavaScript. Todas as operações de banco de dados dentro de scripts são executadas em isolamento de instantâneo. Se ela for uma coleção de partição única, a execução terá o escopo definido para a coleção. Se a coleção estiver particionada, a execução terá o escopo definido para documentos com o mesmo valor de chave de partição dentro da coleção. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Para obter mais informações, consulte a [Programação JavaScript do lado do servidor para o Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa no Cosmos DB?
Você pode fazer a inserção em massa documentos no Azure Cosmos DB de uma das duas maneiras:

* A ferramenta de migração de dados, conforme descrito em [Ferramenta de migração de banco de dados para o Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito em [Programação de JavaScript do lado do servidor para o Azure Cosmos DB](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>A API do DocumentDB dá suporte ao cache de link de recursos?
Sim. Como o Azure Cosmos DB é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Os clientes da API do DocumentDB podem especificar um cabeçalho “If-None-Match” para leituras em qualquer documento semelhante a recurso ou coleção e atualizar suas cópias após a versão do servidor ser alterada.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Uma instância local da API do DocumentDB está disponível?
Sim. O [Emulador do Azure Cosmos DB](local-emulator.md) fornece uma emulação de alta fidelidade do serviço Cosmos DB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o Emulador do Azure Cosmos DB e implanta-los no Azure em escala global fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

## <a name="develop-against-the-api-for-mongodb"></a>Desenvolver com a API para o MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>O que é a API do Azure Cosmos DB para MongoDB?
A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade que permite aos aplicativos se comunicar com facilidade e transparência com o mecanismo de banco de dados nativo do Azure Cosmos DB usando as APIs e os drivers do Apache MongoDB existentes e com suporte na comunidade. Os desenvolvedores agora podem usar cadeias de ferramenta do MongoDB existentes e habilidades para criar aplicativos que aproveitam o Azure Cosmos DB. Os desenvolvedores se beneficiam dos recursos exclusivos do Azure Cosmos DB, que incluem a indexação automática, a manutenção de backup, SLAs (contratos de nível de serviço) com suporte financeiro e assim por diante.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Como fazer para me conectar ao meu banco de dados da API para MongoDB?
A maneira mais rápida de se conectar à API para MongoDB do Azure Cosmos DB é acessar o [Portal do Azure](https://portal.azure.com). Vá para sua conta e, em seguida, no menu de navegação à esquerda, clique em **Início Rápido**. O Início Rápido é a melhor maneira de obter trechos de código para se conectar ao banco de dados. 

O Azure Cosmos DB impõe padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por SSL, portanto, use o TLSv1.2.

Para obter mais informações, veja [Conectar-se ao banco de dados da API para MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Há códigos de erro adicionais para um banco de dados da API para MongoDB?
Além dos códigos de erro do MongoDB comuns, a API do MongoDB tem seus próprios códigos de erro específicos:


| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas excedeu a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere expandir a produtividade da coleção no Portal do Azure ou tente novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exemplo: *&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Desenvolver com a API de Tabela (versão prévia)

### <a name="terms"></a>Termos 
O Azure Cosmos DB: API de Tabela (versão prévia) refere-se à oferta premium do Azure Cosmos DB para o suporte a tabelas divulgado no Build 2017. 

O SDK de tabela standard é o SDK de Tabela do Armazenamento do Azure existente. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Como posso usar a nova oferta de API de Tabela (versão prévia)? 
A API de Tabela do Azure Cosmos DB está disponível no [Portal do Azure][azure-portal]. Primeiro você deve se inscrever para uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de API de Tabela do Azure Cosmos DB à sua assinatura e, em seguida, adicionar tabelas à conta. 

Durante o período de versão prévia, quando os [SDKs](../cosmos-db/table-sdk-dotnet.md) estão disponíveis para o .NET. você pode começar a trabalhar completando o artigo de início rápido da [API de Tabela](../cosmos-db/create-table-dotnet.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>É necessário ter um novo SDK para usar a API de Tabela (versão prévia)? 
Sim, o [SDK da tabela premium do Armazenamento do Microsoft Azure (versão prévia)](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) está disponível no NuGet. Informações adicionais estão disponíveis na página [API .NET de Tabela do Azure Cosmos DB: download e notas de versão](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md). 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como fazer para fornecer comentários sobre o SDK ou bugs?
Você pode compartilhar seus comentários em qualquer uma das seguintes maneiras:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Qual é a cadeia de conexão que preciso usar para me conectar à API de Tabela (versão prévia)?
A cadeia de conexão é:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
Obtenha a cadeia de conexão na página Chaves do portal do Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Como fazer para substituir as definições de configuração das opções de solicitação na nova API de Tabela (versão prévia)?
Para obter informações sobre definições de configuração, consulte [Recursos do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Altere as configurações adicionando-as ao app.config na seção appSettings do aplicativo cliente.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>Há alterações para clientes que estão usando o SDK de tabela padrão existente?
Nenhuma. Não há nenhuma alteração para clientes novos ou existentes que usam o SDK de Tabela Standard existente. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Como fazer para exibir dados de tabela que são armazenados no Azure Cosmos DB para uso com a API de Tabela (versão prévia)? 
Use o portal do Azure para procurar os dados. Você também pode usar o código da API de Tabela (versão prévia) ou as ferramentas mencionadas na próxima resposta. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Quais ferramentas funcionam com a API de Tabela (versão prévia)? 
Você pode usar a versão mais antiga do Azure Explorer (0.8.9).

Ferramentas que têm a flexibilidade para aceitar uma cadeia de conexão no formato especificado anteriormente podem dar suporte à nova API de Tabela (versão prévia). Uma lista das ferramentas de tabela é fornecida na página [Ferramentas de cliente do Armazenamento do Azure](../storage/common/storage-explorers.md). 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>A CLI do Azure ou o PowerShell funciona com a nova API de Tabela (versão prévia)?
Planejamos adicionar suporte para o PowerShell e para a CLI do Azure para a API de Tabela (versão prévia). 

### <a name="is-the-concurrency-on-operations-controlled"></a>A simultaneidade nas operações é controlada?
Sim, a simultaneidade otimista é fornecida com o uso do mecanismo de Etag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Há suporte para o modelo de consulta OData em entidades? 
Sim, a API de Tabela (versão prévia) dá suporte às consultas OData e LINQ. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>Posso me conectar à tabela do Azure standard e à nova API de Tabela premium (versão prévia) lado a lado no mesmo aplicativo? 
Sim, você pode se conectar criando duas instâncias separadas do CloudTableClient, cada uma apontando para seu próprio URI pela cadeia de conexão.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Como fazer para migrar o aplicativo do armazenamento de Tabelas do Azure existente para essa nova oferta?
Para aproveitar a nova oferta da API de Tabela em seus dados de armazenamento de Tabelas existentes, entre em contato com [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>Qual é o roteiro desse serviço e quando vocês oferecerão outra funcionalidade da API de Tabela standard?
Estamos planejando adicionar suporte a tokens de SAS, ServiceContext, Estatísticas, Criptografia do Lado do Cliente, Análise e outros recursos conforme avançamos para a GA. Você pode nos enviar comentários no [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como a expansão do tamanho do armazenamento é feita nesse serviço se, por exemplo, eu começar com *n* GB de dados e meus dados aumentarem para 1 TB ao longo do tempo? 
O Azure Cosmos DB foi projetado para fornecer armazenamento ilimitado com o uso da escala horizontal. O serviço pode monitorar e efetivamente aumentar seu armazenamento. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Como fazer para monitorar a oferta da API de Tabela (versão prévia)?
Use o painel **Métrica** da API de Tabela (versão prévia) para monitorar as solicitações e o uso do armazenamento. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como fazer para calcular a produtividade de que preciso?
Você pode usar o avaliador de capacidade para calcular o TableThroughput necessário para as operações. Para obter mais informações, consulte [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Estimar Unidades de Solicitação e Armazenamento de Dados). Em geral, você pode representar a entidade como JSON e fornecer os números para as operações. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK da API de Tabela (versão prévia) localmente com o emulador?
Sim, você pode usar a API de Tabela (versão prévia) com o emulador local ao usar o novo SDK. Para baixar o novo emulador, acesse [Use the Azure Cosmos DB Emulator for local development and testing](local-emulator.md) (Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste locais). O valor StorageConnectionString no app.config deve ser:

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>Um aplicativo que já tenho pode trabalhar com a API de Tabela (versão prévia)? 
A área de superfície da nova API de Tabela (versão prévia) é compatível com o SDK de Tabela Standard do Azure existente nas operações de criação, exclusão, atualização e consulta na API do .NET. Certifique-se de que você tem uma chave de linha, porque a API de Tabela (versão prévia) requer uma chave de partição e uma chave de linha. Também planejamos adicionar mais suporte do SDK conforme avançamos para a GA dessa oferta de serviço.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Preciso migrar meus aplicativos existentes baseados na Tabela do Azure para o novo SDK caso eu não deseje usar os recursos da API de Tabela (versão prévia)?
Não, você pode criar e usar os ativos presentes da Tabela Standard sem nenhum tipo de interrupção. No entanto, se você não usar a nova API de Tabela (versão prévia), não poderá se beneficiar do índice automático, da opção de consistência adicional ou da distribuição global. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Como fazer para adicionar a replicação dos dados na API de Tabela (versão prévia) Premium em várias regiões do Azure?
Você pode usar as [configurações de replicação global](tutorial-global-distribution-documentdb.md#portal) do portal do Azure Cosmos DB para adicionar regiões que são adequadas para seu aplicativo. Para desenvolver um aplicativo distribuído globalmente, você deve também adicionar seu aplicativo com as informações de PreferredLocation definidas como a região local, a fim de fornecer uma baixa latência de leitura. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Como fazer para alterar a região de gravação primária da conta na API de Tabela (versão prévia) Premium?
Você pode usar o painel do portal de replicação global do Azure Cosmos DB para adicionar uma região e, em seguida, fazer failover para a região desejada. Para obter instruções, consulte [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como fazer para configurar minhas regiões de leitura preferenciais com baixa latência ao distribuir meus dados? 
Para ajudar a leitura do local, use a chave PreferredLocation no arquivo app.config. Para aplicativos existentes, a API de Tabela (versão prévia) gera um erro se LocationMode for definido. Remova esse código, porque a API de Tabela (versão prévia) premium obtém essas informações do arquivo app.config. Para obter mais informações, consulte [Recursos do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Como devo pensar sobre os níveis de consistência na API de Tabela (versão prévia)? 
O Azure Cosmos DB fornece compensações bem fundamentadas entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência para os desenvolvedores da API de Tabela (versão prévia), para que você possa escolher o modelo de consistência certo no nível da tabela e fazer solicitações individuais ao consultar os dados. Quando um cliente se conecta, ele pode especificar um nível de consistência. Você pode alterar o nível por meio da configuração do app.config para o valor da chave TableConsistencyLevel. 

A API de Tabela (versão prévia) fornece leituras de baixa latência com a “Leitura de suas próprias gravações” com a consistência da sessão como padrão. Para obter mais informações, consulte [Níveis de consistência](consistency-levels.md). 

Por padrão, o armazenamento de Tabelas do Azure fornece uma coerência Forte em uma região e uma Eventual nas localizações secundárias. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>O Azure Cosmos DB oferece mais níveis de consistência do que as tabelas standard?
Sim, para obter informações sobre como se beneficiar da natureza distribuída do Azure Cosmos DB, consulte [Níveis de consistência](consistency-levels.md). Como são fornecidas garantias para os níveis de consistência, você pode usá-los com confiança. Para obter mais informações, consulte [Recursos do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global é habilitada, quanto tempo é necessário para replicar os dados?
Confirmamos os dados de forma duradoura na região do local e enviamos por push os dados para outras regiões imediatamente em questão de milissegundos. Essa replicação depende apenas do RTT (tempo de ida e volta) do data center. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, consulte [Azure Cosmos DB: um serviço de banco de dados distribuído globalmente no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência de solicitação de leitura pode ser alterado?
Com o Azure Cosmos DB, você pode definir o nível de consistência no nível do contêiner (na tabela). Usando o SDK, você pode alterar o nível fornecendo o valor para a chave TableConsistencyLevel no arquivo app.config. Os valores possíveis são: Forte, Desatualização Limitada, Sessão, Prefixo Consistente e Eventual. Para obter mais informações, consulte [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](consistency-levels.md). A ideia da chave é que você não pode definir o nível de consistência da solicitação em mais do que a configuração da tabela. Por exemplo, você não pode definir o nível de consistência para a tabela como Eventual e o nível de consistência de solicitação como Forte. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Como uma conta da API de Tabela (versão prévia) Premium lida com o failover caso uma região fique inativa? 
A API de Tabela (versão prévia) Premium empresta da plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o tempo de inatividade do data center, habilite pelo menos mais uma região para a conta no Portal do Azure Cosmos DB [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](regional-failover.md). Defina a prioridade da região usando o portal [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](regional-failover.md). 

Você pode adicionar quantas regiões quiser para a conta e controlar para onde ela pode fazer o failover fornecendo uma prioridade de failover. É claro que, para usar o banco de dados, você precisa fornecer um aplicativo lá também. Quando você fizer isso, os clientes não terão o tempo de inatividade. O SDK do cliente tem hospedagem automática. Ou seja, ele pode detectar a região que está inoperante e automaticamente fazer o failover para a nova região.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>A API de Tabela (versão prévia) Premium está habilitada para backups?
Sim, a API de Tabela (versão prévia) Premium empresta da plataforma do Azure Cosmos DB para os backups. Os backups são feitos automaticamente. Para obter mais informações, consulte [Backup e restauração online com o Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>A API de Tabela (versão prévia) indexa todos os atributos de uma entidade por padrão?
Sim, por padrão, todos os atributos de uma entidade são indexados. Para obter mais informações, consulte [Azure Cosmos DB: indexando políticas](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Isso significa que não é necessário criar vários índices para atender às consultas? 
Sim, o Azure Cosmos DB fornece a indexação automática de todos os atributos sem nenhuma definição de esquema. Essa automação libera os desenvolvedores para se concentrarem no aplicativo, em vez de na criação e no gerenciamento de índices. Para obter mais informações, consulte [Azure Cosmos DB: indexando políticas](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Posso alterar a política de indexação?
Sim, você pode alterar a política de indexação fornecendo a definição de índice. Para obter mais informações, consulte [Recursos do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Você precisa codificar e escapar adequadamente as configurações. 

No formato JSON de cadeia de caracteres no arquivo app.config:
```
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
Na versão prévia, a API de Tabela fornece a mesma funcionalidade de consulta que o armazenamento de Tabelas do Azure. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Forneceremos a funcionalidade adicional na API de Tabela em uma atualização de serviço futura. Para obter mais informações, consulte [Consultas SQL para a API do DocumentDB do Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quando devo alterar a TableThroughput da API de Tabela (versão prévia)?
Você deve alterar TableThroughput quando uma das seguintes condições se aplicar:
* Você estiver executando uma ETL (extração, transformação e carregamento) de dados ou desejar carregar muitos dados em um curto período de tempo. 
* Você precisa de mais produtividade do contêiner no back-end. Por exemplo, você verá que a produtividade usada é maior do que a provisionada e você está sendo limitado. Para obter mais informações, consulte [Definir a taxa de transferência para contêineres do Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Posso escalar verticalmente ou reduzir verticalmente a produtividade da minha tabela da API de Tabela (versão prévia)? 
Sim, você pode usar o painel de escala do portal do Azure Cosmos DB para dimensionar a produtividade. Para obter mais informações, veja [Configurar produtividade](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Há uma configuração padrão de TableThroughput para as tabelas recém-provisionadas?
Sim, se você não substituir a TableThroughput por meio do app.config e não usar um contêiner pré-criado no Azure Cosmos DB, o serviço criará uma Tabela com uma produtividade de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>Há alguma alteração de preço para aqueles que já são clientes da API de Tabela Standard?
Nenhuma. Não há nenhuma alteração de preço para aqueles que já são clientes da API de Tabela Standard. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Como o preço é calculado para a API de Tabela (versão prévia)? 
O preço depende da TableThroughput alocada. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Como fazer para lidar com a limitação nas tabelas na oferta da API de Tabela (versão prévia)? 
Se a taxa de solicitação exceder a capacidade da produtividade provisionada para o contêiner subjacente, você receberá um erro e o SDK tentará realizar a chamada novamente aplicando a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Por que é necessário escolher uma produtividade, além de PartitionKey e RowKey para aproveitar a oferta da API de Tabela (versão prévia) Premium do Azure Cosmos DB?
O Azure Cosmos DB define uma produtividade padrão para o contêiner caso você não forneça uma no arquivo app.config. 

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Configurar TableThroughput garante que você obtenha a produtividade e a latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso operacional. 

Usando a especificação de produtividade, você pode alterá-la de forma elástica para se beneficiar da sazonalidade de seu aplicativo, atender às necessidades de produtividade e reduzir custos.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>O SDK do Armazenamento do Azure foi muito barato para mim, pois pago apenas para armazenar os dados e raramente realizo consultas. A nova oferta do Azure Cosmos DB parece estar me cobrando mesmo sem ter realizado nenhuma transação ou armazenado nada. Vocês podem explicar?

O Azure Cosmos DB foi projetado para ser um sistema baseado em SLA distribuído globalmente com garantias de disponibilidade, latência e produtividade. Quando você reserva a produtividade no Azure Cosmos DB, ela fica garantida, diferentemente da produtividade de outros sistemas. O Azure Cosmos DB fornece recursos adicionais que os clientes solicitaram, como índices secundários e distribuição global. Durante o período de versão prévia, fornecemos um modelo com otimização da produtividade e, eventualmente, planejamos fornecer um modelo com otimização do armazenamento para atender às necessidades de nossos clientes. 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Eu nunca recebi uma notificação de “cota cheia” (indicando que uma partição está cheia) quando realizo a ingestão de dados no armazenamento de tabelas. Com a API de Tabela (versão prévia), eu recebo essa mensagem. Essa oferta está me limitando e me forçando a alterar meu aplicativo existente?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Para assegurar o desempenho premium garantido, certifique-se de que seu índice e tamanho de dados sejam gerenciáveis e escalonáveis. O limite de 10 GB no número de entidades ou itens por chave de partição serve para garantir que fornecemos um excelente desempenho de pesquisa e consulta. Para garantir que seu aplicativo dimensiona bem mesmo para o Armazenamento do Azure, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>PartitionKey e RowKey ainda são necessárias na nova API de Tabela (versão prévia)? 
Sim. Como a área de superfície da API de Tabela (versão prévia) é semelhante à do SDK do armazenamento de tabelas, a chave de partição fornece uma maneira eficiente de distribuir os dados. A chave de linha é exclusiva nessa partição. A chave de linha deve estar presente e não pode ser nula como no SDK Standard. O comprimento da RowKey é de 255 bytes e da PartitionKey é de 100 bytes (a ser aumentado em breve para 1 KB). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Quais são as mensagens de erro da API de Tabela (versão prévia)?
Como essa versão prévia é compatível com a tabela Standard, a maioria dos erros será mapeada para os erros da tabela Standard. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Por sou limitado quando tento criar várias tabelas uma após a outra na API de Tabela (versão prévia)?
O Azure Cosmos DB é um sistema baseado em SLA que fornece garantia de latência, produtividade, disponibilidade e consistência. Como ele é um sistema provisionado, ele reserva recursos para garantir esses requisitos. A rápida taxa de criação de tabelas é detectada e limitada. É recomendável que você examine a taxa de criação de tabelas e a reduza para menos de cinco por minuto. Lembre-se de que a API de Tabela (versão prévia) é um sistema provisionado. No momento em que você a provisionar, começará a pagar por ela. 

## <a name="develop-against-the-graph-api-preview"></a>Desenvolver em relação à API do Graph (versão prévia)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Como posso aplicar a funcionalidade da API do Graph (versão prévia) ao Azure Cosmos DB?
Você pode usar uma biblioteca de extensão para aplicar a funcionalidade da API do Graph (versão prévia). Essa biblioteca é chamada de Gráficos do Microsoft Azure e está disponível no NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Parece que vocês dão suporte à linguagem de percurso do gráfico Gremlin. Vocês planejam adicionar mais formas de consulta?
Sim, planejamos adicionar outros mecanismos para consulta no futuro. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Como posso usar a nova oferta da API do Graph (versão prévia)? 
Para começar, conclua o artigo de início rápido da [API do Graph](../cosmos-db/create-graph-dotnet.md).

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Perguntas de clientes do DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Por que vocês estão mudando para o Azure Cosmos DB? 

O Azure Cosmos DB é o próximo grande salto em bancos de dados em nuvem em escala distribuídos globalmente. Como um cliente do DocumentDB, agora você tem acesso ao sistema e aos recursos inovadores fornecidos pelo Azure Cosmos DB.

O Azure Cosmos DB começou como o "Project Florence" em 2010 para solucionar as adversidades que os desenvolvedores enfrentam na criação de aplicativos de grande escala dentro da Microsoft. Os desafios de criação de aplicativos distribuídos globalmente não são exclusivos da Microsoft, então disponibilizamos a primeira geração dessa tecnologia em 2015 para os desenvolvedores do Azure na forma do Azure DocumentDB. 

Desde então, nós adicionados novos recursos e aumentamos a gama de possibilidades. Como resultado, surgiu o Azure Cosmos DB. Como parte desta versão, os clientes do DocumentDB, com seus dados, automática e totalmente se tornaram clientes do Azure Cosmos DB. Esses recursos estão nas áreas do mecanismo de banco de dados principal, bem como distribuição global, escalabilidade elástica e SLAs líderes do mercado abrangentes. Especificamente, desenvolvemos o mecanismo de banco de dados do Azure Cosmos DB para mapear com eficiência todos os modelos de dados populares, sistemas de tipos e APIs do modelo de dados anterior do Azure Cosmos DB. 

A manifestação atual voltada para o desenvolvedor desse trabalho é o novo suporte para [Gremlin](../cosmos-db/graph-introduction.md) e [APIs de armazenamento de tabelas](../cosmos-db/table-introduction.md). E isso é apenas o começo. Planejamos adicionar outras APIs populares e modelos de dados mais novos ao longo do tempo, com mais aprimoramentos no desempenho e armazenamento em escala global. 

É importante ressaltar que o [dialeto SQL](../documentdb/documentdb-sql-query.md) do DocumentDB sempre foi apenas uma das muitas APIs às quais o Azure Cosmos DB subjacente pode dar suporte. Para os desenvolvedores que usam um serviço gerenciado completo, como o Azure Cosmos DB, a única interface para o serviço são as APIs expostas pelo serviço. Nada muda realmente para os clientes existentes do DocumentDB. No Azure Cosmos DB, você obtém exatamente a mesma API do SQL que o DocumentDB oferece. E agora (e no futuro), você pode acessar outros recursos anteriormente inacessíveis 

Outra manifestação do nosso trabalho contínuo é a base estendida para escalabilidade elástica de uma taxa de transferência e armazenamento globais. Fizemos vários aprimoramentos fundamentais para o subsistema de distribuição global. Um dos muitos recursos voltados para o desenvolvedor é o modelo de consistência de Prefixo Consistente, totalizando cinco modelos de consistência bem-definidos. Lançaremos muitos outros recursos interessantes quando estiverem perfeitamente desenvolvidos. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>O que é devo fazer para garantir que os meus recursos do DocumentDB continuem a ser executados no Azure Cosmos DB?

Você não precisa fazer nenhuma alteração. Agora, os recursos do DocumentDB são recursos do Azure Cosmos DB e não houve interrupção no serviço quando essa mudança ocorreu.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quais alterações devem ser feitas para que o meu aplicativo funcione no Azure Cosmos DB?

Você não precisa fazer nenhuma alteração. Nomes de pacote do NuGet, namespaces e classes não foram alterados. Como sempre, é recomendável que você mantenha seus SDKs atualizados para tirar proveito dos recursos e aprimoramentos mais recentes. 

### <a name="whats-changed-in-the-azure-portal"></a>O que mudou no portal do Azure?

O DocumentDB não aparece mais no portal como um serviço do Azure. Em seu lugar, há um novo ícone do Azure Cosmos DB, conforme mostrado na imagem a seguir. Todas as coleções estão disponíveis, como sempre estiveram e você ainda pode dimensionar a produtividade, alterar os níveis de consistência e monitorar SLAs. Os recursos do Data Explorer (versão prévia) foram aprimorados. Agora, você pode exibir e editar documentos, criar e executar consultas e trabalhar com procedimentos armazenados, gatilhos e UDF de uma página, conforme mostrado na imagem a seguir: 

![A folha Coleções do Azure Cosmos DB](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Há alterações de preços?

Não, o custo da execução do aplicativo no Azure Cosmos DB continua o mesmo.

### <a name="are-there-changes-to-the-slas"></a>Há alterações nos SLAs?

Não, os SLAs para disponibilidade, consistência, latência e taxa de transferência continuam os mesmos e são exibidos no portal. Para obter mais informações, veja [SLA para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicativo de tarefas pendentes com os dados de exemplo](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
